---

title: "OTEL learning on review"
keywords: otel
sidebar: mydoc_sidebar
permalink: otel-review.html
last_updated: Oct 8, 2026
comments: true

---


# Batch ETL Telemetry 리뷰 — 내가 헷갈렸고 배운 것들

> 배경: Node/TypeScript로 만든 batch ETL CLI(`etl-job`)에 run telemetry를 붙이는 PR을 리뷰받으면서 헷갈렸던 질문과 답을 정리한 노트.
> `etl-job`은 CMS와 product API에서 content를 읽어 검색 엔진의 alias 뒤 collection에 load한다.
> Kubernetes CronJob으로 돈다. Trace/metric은 OTLP로 collector에 보내고 Grafana Tempo/Prometheus에서 본다. Log는 stdout으로 내보내 Loki에서 본다.
>
> 목적: 구현 자체보다 **mental model**을 남겨서, 다음 코드리뷰에서 같은 종류의 과설계를 빨리 발견하기.
> 각 주제는 "짧은 설명 → 예제 코드 → takeaway" 순서다. 예제는 단순화한 illustrative code이고 실제 코드가 아니다.

---

## 목차

Part 1 — 리뷰에서 헷갈렸던 것

- [0. 전체적으로 배운 핵심](#0-전체적으로-배운-핵심)
- [1. Job / Run / Trace / Span / Pod 관계](#1-job--run--trace--span--pod-관계)
- [2. Metric은 Trace와 다른 축이다](#2-metric은-trace와-다른-축이다)
- [3. Pino: logger / serializer / redact](#3-pino-logger--serializer--redact)
- [4. `Telemetry` object는 Singleton인가 Namespace인가?](#4-telemetry-object는-singleton인가-namespace인가)
- [5. 검색 엔진 Collection / Alias / Swap](#5-검색-엔진-collection--alias--swap)
- [6. Orphan collection이 무엇인가](#6-orphan-collection이-무엇인가)
- [7. alias가 안 가리키는 collection을 그냥 지우면 안 되는 이유](#7-alias가-안-가리키는-collection을-그냥-지우면-안-되는-이유)
- [8. `concurrencyPolicy: Forbid`가 왜 중요했나](#8-concurrencypolicy-forbid가-왜-중요했나)
- [9. Swap에서 실제로 필요한 safety](#9-swap에서-실제로-필요한-safety)
- [10. 이번 PR에서 배운 코드리뷰 질문](#10-이번-pr에서-배운-코드리뷰-질문)
- [11. 한 페이지 요약](#11-한-페이지-요약)
- [12. 가장 중요한 리뷰 습관](#12-가장-중요한-리뷰-습관)

Part 2 — 같은 PR에서 나온 관련 개념 (예제 중심)

- [13. Telemetry는 write-only다](#13-telemetry는-write-only다)
- [14. Run summary object는 run이 소유한다](#14-run-summary-object는-run이-소유한다)
- [15. Count는 한 곳에서만 센다](#15-count는-한-곳에서만-센다)
- [16. Generator는 lazy하다](#16-generator는-lazy하다)
- [17. Skip-and-count helper와 `yield`의 위치](#17-skip-and-count-helper와-yield의-위치)
- [18. 성공한 span 버리기: SpanProcessor filter](#18-성공한-span-버리기-spanprocessor-filter)
- [19. Signal 처리, flush, exit code](#19-signal-처리-flush-exit-code)
- [20. Circular import (config ↔ logger)](#20-circular-import-config--logger)
- [21. Layered architecture: import는 아래로만](#21-layered-architecture-import는-아래로만)
- [22. 코드를 지우기 전의 evidence rule](#22-코드를-지우기-전의-evidence-rule)
- [23. OTel 환경 변수: chart vs local `.env`](#23-otel-환경-변수-chart-vs-local-env)
- [24. In-cluster collector vs cluster 밖 서비스](#24-in-cluster-collector-vs-cluster-밖-서비스)
- [25. Git: commit별 검증, hunk 분리, merge vs rebase](#25-git-commit별-검증-hunk-분리-merge-vs-rebase)

---

# Part 1 — 리뷰에서 헷갈렸던 것

## 0. 전체적으로 배운 핵심

이번 PR에서 반복해서 나온 패턴은 하나였다.

> **문제 자체는 실제일 수 있지만, 해결책의 크기가 문제보다 훨씬 커질 수 있다.**

그래서 코드를 읽기 전에 다음을 먼저 확인한다.

1. 라이브러리가 이미 제공하는 기능이 있는가?
2. Kubernetes 같은 플랫폼이 이미 보장하는 것이 있는가?
3. 이 edge case가 실제 production에서 일어나는가?
4. 일어나더라도 정말 availability/data loss까지 가는가, 아니면 한 번의 run이 실패하는 정도인가?
5. 코드 크기가 해결하려는 개념의 크기에 비례하는가?

---

## 1. Job / Run / Trace / Span / Pod 관계

처음에는 `Pod -> Job -> Run -> Trace -> Span` 같은 계층으로 생각하기 쉽다. 정확히는 그렇지 않다.

### 용어 먼저

- **Trace**: 한 번의 작업 흐름 전체. 여기서는 run 한 번.
- **Span**: trace 안의 한 단계. 시작/끝 시간, 이름, 부모 span을 가진다.
- **Attribute**: span에 붙이는 key/value. 예: `etl.items.loaded = 812`.
- **Event**: span 안의 특정 시점에 찍는 기록. 예: "page 3 fetched".

### Mental model

```text
Job definition
   │
   ├─ Run 1
   │    └─ Trace
   │         ├─ Span
   │         ├─ Span
   │         └─ Span
   │
   ├─ Run 2
   │    └─ Trace
   │         └─ ...
   │
   └─ Run N
```

일반적으로:

```text
Job definition → Runs (1:N)
Run            → Trace (보통 1:1)
Trace          → Spans (1:N)
```

`Pod`은 이 논리적인 observability hierarchy의 부모가 아니다. **그 run을 실행하는 runtime/container 단위**에 가깝다.

### 예제: run 하나 = trace 하나

```ts
import { trace, SpanStatusCode } from "@opentelemetry/api";

const tracer = trace.getTracer("etl-job");

async function runOnce() {
  // root span: 이 span이 trace를 시작한다
  await tracer.startActiveSpan("etl.run", async (runSpan) => {
    try {
      await tracer.startActiveSpan("fetch.cms", async (span) => {
        const pages = await fetchPage(1);
        span.setAttribute("etl.items.fetched", pages.length); // attribute
        span.addEvent("page fetched", { page: 1 });            // event
        span.end();
      });

      await tracer.startActiveSpan("load.search", async (span) => {
        await loadBehindAlias("pages");
        span.end();
      });
    } catch (err) {
      runSpan.recordException(err as Error); // 예외 기록 (catch 변수는 unknown)
      runSpan.setStatus({ code: SpanStatusCode.ERROR });
      throw err;
    } finally {
      runSpan.end();
    }
  });
}
```

Tempo에서 보면 waterfall은 대략 이렇다.

```text
etl.run        |==========================================| 42.0s
  fetch.cms    |=============|                              12.3s
  load.search                 |==========================|  29.5s
```

`startActiveSpan` 안에서 만든 span은 자동으로 바깥 span의 child가 된다.

### 내가 헷갈렸던 것

> "Pod 1-N Jobs 1-N Runs 1-N Traces 1-N Spans?"

### 수정된 mental model

- Job은 실행 정의
- Run은 Job이 실제로 한 번 실행된 것
- Trace는 한 run의 흐름을 추적
- Span은 trace 내부의 개별 단계
- Pod은 Kubernetes 실행 환경이지 trace hierarchy가 아님

**Takeaway:** run → trace → spans. Pod은 "어디서 돌았나"이지 "누구의 자식인가"가 아니다.

---

## 2. Metric은 Trace와 다른 축이다

Trace와 Metric을 같은 hierarchy 안에 넣으려고 하면 헷갈린다.

```text
Tracing
Run → Trace → Span

Metrics
metric name + labels → time series
```

둘은 서로 correlation될 수는 있지만 데이터 모델이 다르다.

### 2.1 Gauge

Gauge는 **현재 값 / 최신 값**을 기록한다. 값이 올라가도 되고 내려가도 된다.

```ts
runDuration.set(42);
runDuration.set(30);
runDuration.set(55);
```

예:

```text
memory_usage = 512 MB
active_jobs = 3
last_run_duration = 42 s
```

OpenTelemetry JS에서는 synchronous gauge를 이렇게 쓴다.

```ts
import { metrics } from "@opentelemetry/api";

const meter = metrics.getMeter("etl-job");
const lastRunDuration = meter.createGauge("etl_run_duration_seconds");

lastRunDuration.record(42, { collection: "pages" });
```

내가 처음 이해한 표현:

> "singleton 어딘가에 값을 계속 업데이트하는 느낌?"

큰 방향은 맞다. 다만 구현상 꼭 JS singleton이라는 뜻은 아니다. Metric backend 관점에서 **같은 metric name + label set의 time series에 값이 기록되는 것**이다.

### 2.2 Counter

Counter는 누적값이다. 정상적으로는 감소하지 않는다.

```ts
requests.add(1);
requests.add(1);
requests.add(1);
```

```text
1 → 2 → 3 → 4 ...
```

예:

```text
requests_total
jobs_failed_total
documents_indexed_total
```

#### Gauge와 차이

```text
Gauge   = 지금 몇 개인가?
Counter = 지금까지 몇 번 발생했는가?
```

```text
active_jobs = 3          // Gauge
jobs_started_total = 92  // Counter
```

### 2.3 Histogram

Histogram은 값을 stack처럼 raw로 전부 저장하는 게 아니다.

예를 들어 request duration을 이렇게 기록하면:

```text
20ms
40ms
150ms
900ms
```

보통 내부적으로 bucket/count/sum 형태로 집계한다.

```text
<= 50ms   : 2
<= 100ms  : 2
<= 500ms  : 3
<= 1000ms : 4

count = 4
sum   = 1110ms
```

```ts
const pageFetch = meter.createHistogram("etl_page_fetch_ms");
pageFetch.record(150, { source: "cms" });
```

적합한 경우:

```text
request latency distribution
run duration distribution
batch processing duration distribution
```

### 2.4 Summary

Summary도 distribution을 다룬다. 다만 일반적으로 애플리케이션 쪽에서 quantile을 계산한다.

```text
p50
p90
p99
```

Prometheus/OpenTelemetry 환경에서는 많은 경우 Histogram이 더 일반적이고 aggregation에도 유리하다.

### 2.5 언제 무엇을 쓰는가

| 질문 | Metric |
|---|---|
| 지금 active job이 몇 개인가? | Gauge |
| 지금까지 job이 몇 번 실패했나? | Counter |
| job duration 분포가 어떤가? | Histogram |
| 현재/마지막 run duration 값이 얼마인가? | Gauge |
| p95를 애플리케이션에서 직접 계산해야 하나? | Summary 가능 |

### 2.6 짧게 사는 job pod에는 Gauge가 맞다

CronJob pod은 몇 분 살고 사라진다. Counter는 pod마다 0부터 다시 시작한다. 그래서 series가 run마다 새로 생기고, 샘플이 한두 개뿐이다. `increase()`는 같은 series 안의 증가량을 보므로 이런 series에서는 0이나 extrapolate된 이상한 값이 나온다.

대신 run이 끝날 때 결과를 gauge로 한 번 record하고, "마지막 값"을 읽는다.

```ts
// run 끝에서 한 번
itemsLoaded.record(summary.loaded, { collection: "pages" });
itemsFailed.record(summary.failed, { collection: "pages" });
```

```promql
# 좋음: 지난 1일 동안 마지막으로 보고된 값
last_over_time(etl_items_loaded[1d])

# 짧은 pod의 counter에서는 믿기 어려움
increase(etl_items_loaded_total[1d])
```

**Takeaway:** 짧게 사는 batch job은 "run 결과"를 gauge로 남기고 `last_over_time`으로 읽는다.

### 2.7 중요한 것: cardinality

`run_id`, `trace_id`, `span_id` 같이 거의 매번 다른 값을 metric label로 넣으면 cardinality가 너무 높아진다.

```ts
// 나쁨: run마다 새 time series가 생긴다
itemsLoaded.record(812, { run_id: runId });

// 좋음: 값의 종류가 적은 label만
itemsLoaded.record(812, { collection: "pages" });
```

이런 ID는 보통 Trace와 Log에서 사용한다.

---

## 3. Pino: logger / serializer / redact

### 내가 처음 이해한 것

> "결국 pino는 serializer이고, denylist/allowlist로 추가하거나 빼는 거네."

거의 맞지만 정확히는:

> **Pino는 logger이고, 그 안에 serializer와 redact 기능이 있다.**

### 3.1 Logger의 기본 흐름

```ts
logger.error(
  {
    err,
    jobId: "123",
  },
  "search import failed",
);
```

Pino는 이것을 JSON log로 만든다.

```json
{
  "level": "error",
  "jobId": "123",
  "err": {
    "type": "AxiosError",
    "message": "...",
    "stack": "...",
    "config": {
      "headers": {
        "X-API-KEY": "<secret>"
      }
    }
  },
  "msg": "search import failed"
}
```

문제는 `err`에 너무 많은 값이 들어갈 수 있다는 것.

### 3.2 Serializer

Serializer는 **"이 값을 로그에 어떤 형태로 쓸 것인가?"** 를 결정한다.

```ts
serializers: {
  err: (error) => {
    return {
      type: ...,
      message: ...,
      stack: ...,
    };
  }
}
```

```text
Error object
   ↓
serializer
   ↓
loggable object
```

### 3.3 Redact = denylist

```ts
import pino from "pino";

const logger = pino({
  redact: {
    paths: [
      "err.config",
      "err.request",
      "err.response",
      "err.httpBody",
    ],
    remove: true,
  },
});
```

개념적으로:

```text
모든 field 허용
    ↓
config     제거
request    제거
response   제거
httpBody   제거
그 외      허용
```

이것이 **denylist**다.

장점:

- 간단하다.
- Pino built-in 기능이다.

단점: 나중에 새로운 민감 field가 생기면 denylist에 없으므로 그대로 로그에 나갈 수 있다.

```ts
err.credentials // denylist에 없음 → 로그에 나감
```

### 3.4 Serializer allowlist

```ts
import pino from "pino";

const logger = pino({
  serializers: {
    err: (e) => {
      const { type, message, stack, code } = pino.stdSerializers.err(e);
      return {
        type,
        message,
        stack,
        code,
        httpStatus: e?.httpStatus,
      };
    },
  },
});
```

결과:

```text
type        허용
message     허용
stack       허용
code        허용
httpStatus  허용
나머지      제거
```

즉 **allowlist**다. 새 field가 추가되어도 자동으로 drop된다.

**Takeaway:** 민감 정보가 섞일 수 있는 object(HTTP client error 등)는 allowlist serializer가 기본값으로 더 안전하다.

### 3.5 실제 leak은 존재했다

검색 엔진 network error에서 HTTP client(Axios) error가 그대로 올라오면 이런 값이 로그로 나갈 수 있었다.

```text
err.config.headers["X-API-KEY"]
err.config.data
err.request
```

검색 엔진의 4xx/5xx에서는 `httpBody`에 import batch 전체가 들어갈 수 있었다.

따라서:

> **보안 concern 자체는 실제였다.**

문제는 해결을 위해 custom error transformation helper를 약 100줄 만들 필요가 없었다는 것.

### 3.6 내가 구현했던 것

기존 구현:

```text
Error
 ↓
custom cause-chain parsing
 ↓
custom type detection
 ↓
custom truncation
 ↓
custom field extraction
 ↓
custom sanitization
 ↓
Pino
```

원하는 결과는 이것으로 충분했다.

```text
Error
 ↓
pino.stdSerializers.err()
 ↓
5개 field만 return
```

#### 핵심 교훈

> **Concern real, fix oversized.**

문제가 진짜인지와 해결책이 적절한 크기인지는 별개의 질문이다.

### 3.7 `traceContext`는 redaction과 별개

```ts
mixin: traceContext
```

이것은 error sanitization이 아니다. 모든 로그에 trace id를 붙이는 기능이다.

```ts
import { trace } from "@opentelemetry/api";
import pino from "pino";

function traceContext() {
  const span = trace.getActiveSpan();
  if (!span) return {};
  const { traceId, spanId } = span.spanContext();
  return { trace_id: traceId, span_id: spanId };
}

const logger = pino({ mixin: traceContext });
```

```json
{
  "trace_id": "...",
  "span_id": "...",
  "msg": "page fetched"
}
```

Loki에서 이 log를 찾고, `trace_id`로 Tempo의 trace로 건너갈 수 있다.

```logql
{app="etl-job"} | json | trace_id="<trace id>"
```

```text
serializer/redact
= 무엇을 로그에 쓸지

traceContext mixin
= 어떤 trace에서 나온 로그인지 correlation 정보 추가
```

둘을 같은 문제로 보면 안 된다.

---

## 4. `Telemetry` object는 Singleton인가 Namespace인가?

PR:

```ts
export const Telemetry = {
  start,
  shutdown,
  withChildSpan,
  // ...
};
```

사용:

```ts
Telemetry.start();
Telemetry.shutdown();
Telemetry.withChildSpan();
```

처음에는 "`Telemetry`를 singleton instance처럼 만들려고 한 것인가?"라고 생각했다.

### 결론

**겉모습은 singleton object처럼 보이지만 의도는 namespace에 더 가깝다.**

### 4.1 Singleton의 핵심

Singleton은 **state를 가진 instance가 하나만 존재함을 보장**하는 pattern이다.

```ts
class Telemetry {
  private static instance = new Telemetry();

  private sdk: SDK;

  static getInstance() {
    return this.instance;
  }
}
```

여기서는 `Telemetry` instance 자체가 중요하다.

### 4.2 현재 코드는 함수 묶음

```ts
export const Telemetry = {
  start,
  shutdown,
  withChildSpan,
};
```

이것은 실질적으로 **여러 함수를 `Telemetry.xxx()`라는 이름 아래 묶는 API**다.

```text
Telemetry = stateful singleton instance   (X)
Telemetry = functions grouped under one name   (O)
```

### 4.3 더 자연스러운 ES module namespace

```ts
// src/telemetry/index.ts
export * as Telemetry from "./run";
```

```ts
// src/telemetry/run.ts
export function start() {}
export function shutdown() {}
export function withChildSpan() {}
```

```ts
// 사용 측
import { Telemetry } from "./telemetry";

Telemetry.start();
Telemetry.shutdown();
```

동일하게 namespace 형태를 얻으면서, object를 직접 만들어 16개 함수를 나열할 필요가 없다. 함수를 추가하면 export만 하면 된다.

#### 핵심 교훈

`export const X = { ...functions }`를 보면 먼저 묻는다.

> 이 객체가 정말 stateful object여야 하나?
> 아니면 단순 namespace인가?

---

## 5. 검색 엔진 Collection / Alias / Swap

이 부분이 가장 많이 헷갈렸다.

### 5.1 Collection과 Alias

```text
Collection = 실제 index
Alias      = collection을 가리키는 pointer/name
```

```text
pages alias
      ↓
pages-300
```

사용자는 `pages`로 검색하지만 실제 데이터는 `pages-300`에 있다.

이 검색 엔진에는 collection rename이 없다. 그래서 alias로 이름을 고정한다.

### 5.2 Zero-downtime rebuild

현재:

```text
pages
   ↓
pages-300
```

새 index를 만들 때:

```text
1. pages-400 생성
2. pages-400에 최신 데이터 import
3. alias를 300 → 400으로 변경
4. old collection 300 삭제
```

```text
Before
pages → 300

Building
pages → 300
        400 ← import 중

Swap
pages → 400

Cleanup
300 삭제
```

검색 서비스는 import하는 동안에도 계속 300을 사용하므로 zero downtime이 가능하다.

### 예제: `loadBehindAlias`

```ts
async function loadBehindAlias(alias: string, docs: AsyncIterable<Doc>) {
  const next = `${alias}-${Date.now()}`;          // 1. 새 collection 이름
  await search.createCollection(next, schema);

  for await (const batch of chunk(docs, 500)) {    // 2. import
    await search.importDocuments(next, batch);
  }

  const previous = await search.getAliasTarget(alias); // 없으면 undefined
  await search.upsertAlias(alias, next);           // 3. swap

  if (previous) await search.deleteCollection(previous); // 4. cleanup
}
```

**Takeaway:** 사용자는 항상 alias를 본다. 새 collection은 완성된 다음에만 보인다.

---

## 6. Orphan collection이 무엇인가

```text
pages → 300

새 run:
400 생성
400 import 60%
process crash
```

alias swap 전에 죽으면:

```text
pages → 300

400   ← 아무 alias도 가리키지 않음
```

이 `400`이 orphan이다.

### 왜 cleanup 하는가?

처음 의문:

> "300이 멀쩡한데 400이 남는 게 왜 중요한가?"

정답:

> **availability 때문에 반드시 지우는 것이 아니라, 실패한 rebuild의 찌꺼기가 계속 쌓이기 때문**이다.

```text
100 orphan
200 orphan
300 active
400 orphan
500 orphan
```

계속 남으면:

- storage 낭비
- collection 수 증가
- 운영 시 어떤 collection이 유효한지 혼란
- 실패 찌꺼기가 계속 누적

그래서 housekeeping으로 cleanup한다.

---

## 7. alias가 안 가리키는 collection을 그냥 지우면 안 되는 이유

정상 rebuild 중에도 새 collection은 아직 alias가 가리키지 않는다.

```text
pages → 300

400 생성
400 import 중
```

이 시점의 `400`은 `unreferenced = true`이지만 orphan은 아니다. 현재 정상적으로 build 중이다.

### 7.1 Concurrent cleanup이 400을 지우면?

```text
Run A
400 생성
400 import 60%

Run B 시작
"alias가 안 가리키는 collection 삭제"
→ 400 삭제
```

Run A는 다음 import에서 실패한다. 하지만 `pages → 300`은 그대로다.

#### 내가 헷갈렸던 핵심

> "그럼 300 쓰면 되잖아? 400 실패하면 이전 걸 계속 주면 되잖아?"

맞다. **이미 정상적인 old collection이 있다면 availability는 보통 유지된다.**

```text
alias → 300
400 build 실패
↓
서비스는 계속 300 사용
```

문제는:

```text
최신 데이터 반영 실패
이번 rebuild 실패
다음 cron까지 stale data 사용
```

즉 이 문제는 대부분:

```text
Availability 문제                (X)
Rebuild correctness / freshness 문제   (O)
```

### 7.2 Availability에 영향을 줄 수 있는 예외: 최초 build

아직 `alias → 아무것도 없음`인 상황에서 최초 collection build가 실패하면 검색할 기존 collection이 없다. 따라서 최초 구축에서는 availability 문제가 될 수 있다.

---

## 8. `concurrencyPolicy: Forbid`가 왜 중요했나

```yaml
apiVersion: batch/v1
kind: CronJob
metadata:
  name: etl-job
spec:
  schedule: "0 * * * *"
  concurrencyPolicy: Forbid
  jobTemplate:
    spec:
      backoffLimit: 0
      template:
        spec:
          restartPolicy: Never
          containers:
            - name: etl
              image: registry.example.internal/etl-job:latest
```

`concurrencyPolicy: Forbid`의 의미:

```text
Run A가 아직 실행 중
↓
다음 schedule 도착
↓
Run B 시작하지 않음
```

즉 scheduled run끼리는 동시에 실행되지 않는다.

### 8.1 이 보장이 있으면 cleanup이 단순해진다

새 run이 시작했다는 것은 **이전 scheduled run은 실행 중이지 않다**는 뜻이다.

따라서 start-time cleanup에서 발견한 collection, 즉

```text
pages-<timestamp>
+ 아무 alias도 가리키지 않음
```

은 현재 다른 run이 build 중인 collection일 가능성이 없다.

```ts
// run 시작 시 한 번. Forbid 덕분에 다른 run이 build 중일 수 없다.
async function sweepOrphans(alias: string) {
  const active = await search.getAliasTarget(alias);
  const all = await search.listCollections();
  for (const name of all) {
    if (name.startsWith(`${alias}-`) && name !== active) {
      await search.deleteCollection(name);
    }
  }
}
```

그래서 다음과 같은 방어 로직을 대부분 제거할 수 있다.

```text
age threshold
publication state
switch tracking
cancel-ignoring wrapper
...
```

### 8.2 중요한 사고방식

기존 접근:

```text
Application code가
"다른 run이 build 중인지"
"이 collection이 정말 orphan인지"
"몇 분 이상 됐는지"
...
를 직접 추론
```

Reviewer의 접근:

```text
Kubernetes에서 애초에 concurrent run을 금지
↓
Application cleanup은 단순하게
```

#### 핵심 교훈

> **플랫폼이 invariant를 보장할 수 있으면 애플리케이션에서 그 invariant를 재구현하지 않는다.**

---

## 9. Swap에서 실제로 필요한 safety

기존 main 코드에도 중요한 safety가 이미 있었다.

```text
Client → "alias를 400으로 바꿔줘"
검색 엔진 → 실제로 성공
response 돌아오는 중 network error
```

Client 입장에서는 **성공했는지 실패했는지 모름**이 된다.

그래서 실패하면 read-back으로 실제 state를 확인한다.

```ts
async function swapAlias(alias: string, next: string) {
  try {
    await search.upsertAlias(alias, next);
  } catch (err) {
    // 요청은 성공했는데 응답만 잃었을 수 있다
    if (await aliasPointsAt(alias, next)) return;
    throw err;
  }
}

async function aliasPointsAt(alias: string, name: string) {
  return (await search.getAliasTarget(alias)) === name;
}
```

이것은 의미 있는 safety다.

반면 다음은 `Forbid + start cleanup`으로 상당 부분 제거할 수 있었다.

```text
publication tracking
cancel-ignoring wrapper
switch tracking
age threshold
```

---

## 10. 이번 PR에서 배운 코드리뷰 질문

코드가 커 보일 때 바로 implementation detail부터 읽지 않는다.

### 10.1 Dependency가 이미 제공하는가?

Pino 사례: `100줄 custom sanitization`을 보기 전에 `Pino serializer? Pino redact?`를 먼저 확인해야 했다.

### 10.2 Platform이 이미 해결하는가?

Cleanup 사례: `복잡한 concurrent-build detection`을 만들기 전에 `Kubernetes concurrencyPolicy?`를 확인해야 했다.

### 10.3 코드 크기가 개념 크기에 비례하는가?

"alias를 새 collection으로 switch"하는 개념인데 파일이 751줄이라면 강한 smell이다.

항상 본다.

```text
main 버전은 몇 줄인가?
추가된 function은 몇 개인가?
각 function이 정말 필요한가?
```

```bash
# main 대비 파일 크기와 추가된 함수 수를 빠르게 본다
git show main:src/search/swap.ts | wc -l
wc -l src/search/swap.ts
git diff main -- src/search/swap.ts | grep -c '^+.*function '
```

### 10.4 Edge case는 likelihood × impact로 본다

`동시 cleanup 때문에 새 build 삭제`를 단순히 "가능하다"로 끝내지 않는다.

```text
실제로 얼마나 자주 발생?
production에서 가능한가?
발생하면 availability인가?
data loss인가?
한 번의 rebuild 실패인가?
다음 cron에서 회복 가능한가?
```

이번 경우 old alias가 존재한다면 대부분:

```text
서비스 계속 동작
+
최신 데이터 반영만 늦어짐
```

이었다.

---

## 11. 한 페이지 요약

### Observability

```text
Job → Runs
Run → Trace
Trace → Spans

Pod = runtime
Metric = tracing과 별도 축
```

### Metrics

```text
Gauge     = 현재값
Counter   = 누적 횟수
Histogram = 값의 분포를 bucket/count/sum으로 집계
Summary   = quantile 중심 distribution
```

### Pino

```text
Pino = logger

serializer
= object를 log object로 변환

redact
= 특정 path 제거
= denylist

custom serializer returning selected fields
= allowlist
```

### Telemetry export

```text
export const Telemetry = { fn1, fn2, ... }
≈ hand-built namespace

export * as Telemetry from "./run"
= module namespace

Singleton
= 하나의 stateful instance를 보장
```

### Collection / Alias

```text
collection = index
alias      = pointer

300 active
↓
400 build
↓
alias 300 → 400
↓
300 delete
```

### Orphan

```text
build 중 crash
↓
alias가 가리키지 않는 collection 남음
↓
storage/운영 찌꺼기
```

### Concurrency

```text
Forbid 없음:
cleanup이 다른 run의 build를 지울 수 있음

Forbid 있음:
start-time unreferenced cleanup을 단순하게 할 수 있음
```

### Availability

```text
alias → 300
400 build 실패

=> 보통 availability 유지
=> 300 계속 사용
=> 문제는 freshness/rebuild failure

예외:
최초 build라 기존 collection이 없으면 availability 영향 가능
```

---

## 12. 가장 중요한 리뷰 습관

다음 세 질문을 먼저 한다.

```text
1. 이 문제는 진짜인가?
2. 이 문제를 dependency/platform이 이미 해결하는가?
3. 이 정도 위험을 해결하는 데 이 정도 코드가 정말 필요한가?
```

이번 PR의 대표적인 결론:

```text
Logging:
문제 진짜
→ 해결은 5줄이면 됨

Telemetry namespace:
기능 문제 아님
→ 더 자연스러운 module export 가능

Orphan collection:
문제 진짜
→ 하지만 availability catastrophe는 아님
→ Kubernetes Forbid + start cleanup이면 충분
```

---

# Part 2 — 같은 PR에서 나온 관련 개념 (예제 중심)

## 13. Telemetry는 write-only다

Span과 metric은 밖으로 내보내는 출력이다. OpenTelemetry API의 `Span`에는 attribute를 읽는 getter가 없다. 그래서 앱 로직은 span에서 값을 읽으면 안 되고, 평범한 변수에서 읽는다.

```ts
// 나쁨: span을 state 저장소처럼 쓰려는 시도. API에 getter가 없다.
span.setAttribute("etl.items.loaded", 10);
// const n = span.getAttribute("etl.items.loaded"); // 존재하지 않음

// 좋음: 앱 로직은 변수를 읽고, telemetry에는 쓰기만 한다
let loaded = 0;
for (const doc of docs) {
  await index(doc);
  loaded++;
}
if (loaded === 0) throw new Error("nothing loaded"); // 로직은 변수를 본다
span.setAttribute("etl.items.loaded", loaded);       // telemetry는 결과만 받는다
```

이유: telemetry가 꺼져 있거나(no-op SDK) export가 실패해도 job 동작은 같아야 한다.

**Takeaway:** 앱 → telemetry 방향으로만 흐른다. telemetry → 앱 로직으로는 절대 안 흐른다.

---

## 14. Run summary object는 run이 소유한다

Run 하나의 결과(어떤 table, 몇 개 load, 몇 개 실패)를 object 하나에 모은다. Run이 만들고, 단계들이 채우고, run이 끝날 때 log line 한 줄과 gauge로 내보낸다.

```ts
type RunSummary = {
  startedAt: number;
  collection?: string;
  fetched: number;
  loaded: number;
  failed: number;
};

function startRun(): RunSummary {
  return { startedAt: Date.now(), fetched: 0, loaded: 0, failed: 0 };
}

function finishRun(s: RunSummary, outcome: "success" | "failure") {
  const durationS = (Date.now() - s.startedAt) / 1000;
  logger.info({ ...s, outcome, durationS }, "etl_run_finished");   // finish line
  runDuration.record(durationS, { collection: s.collection ?? "none" });
  itemsLoaded.record(s.loaded, { collection: s.collection ?? "none" });
  itemsFailed.record(s.failed, { collection: s.collection ?? "none" });
}

// 사용
const summary = startRun();
try {
  summary.collection = "pages";             // load 단계가 table 이름을 정한다
  for await (const doc of fetchAll()) {
    summary.fetched++;
    await index(doc);
    summary.loaded++;
  }
  finishRun(summary, "success");
} catch (err) {
  finishRun(summary, "failure");
  throw err;
}
```

Loki에서 한 줄로 run 결과를 찾는다.

```logql
{app="etl-job"} | json | msg="etl_run_finished" | outcome="failure"
```

**Takeaway:** "이번 run이 어땠나"는 summary 하나를 보면 답이 나와야 한다.

---

## 15. Count는 한 곳에서만 센다

Logger, span, metric이 각자 count를 세면 두 숫자가 서로 어긋난다. 그러면 dashboard와 log 중 어느 쪽을 믿어야 할지 모른다.

```ts
// 나쁨: counter 두 개가 따로 논다
metricLoaded.add(1);      // 한 곳
logLoadedCount++;         // 다른 곳, 어떤 path에서는 빠짐

// 좋음: summary 하나만 증가시키고, 끝에서 모두 같은 값을 읽는다
summary.loaded++;
// ... finishRun()에서 log, span attribute, gauge가 summary.loaded를 같이 읽음
```

**Takeaway:** 숫자는 한 곳에서 세고, 출력은 여러 곳에서 같은 값을 읽는다.

---

## 16. Generator는 lazy하다

`function*` 본문은 호출할 때가 아니라 첫 `next()`가 불릴 때 실행된다. 그래서 generator 안에서 던진 에러도 소비하는 쪽에서 터진다.

```ts
function* pages() {
  console.log("body starts");
  yield 1;
  yield 2;
}

const it = pages();       // 아무것도 출력되지 않는다
console.log("created");
it.next();                // 여기서 "body starts"
```

출력:

```text
created
body starts
```

**Takeaway:** generator를 만든 곳에 try/catch를 두면 아무것도 잡지 못한다. 실행은 `for...of`가 도는 곳에서 일어난다.

---

## 17. Skip-and-count helper와 `yield`의 위치

Item 하나가 깨졌다고 run 전체를 죽이지 않는다. Item마다 try/catch로 감싸고, 실패하면 log(어떤 item, 어떤 단계, 왜)를 남기고, count하고, 건너뛴다.

```ts
function* mapSkipping<T, U>(
  items: Iterable<T>,
  step: string,
  fn: (item: T) => U,
  summary: RunSummary,
) {
  for (const item of items) {
    let out: U;
    try {
      out = fn(item);                 // 이 item의 작업만 try 안에
    } catch (err) {
      logger.warn({ err, step, itemId: idOf(item) }, "etl_item_failed");
      summary.failed++;
      continue;                       // skip
    }
    yield out;                        // yield는 try 밖
  }
}
```

`yield`를 try 안에 두면 안 되는 이유: `yield`는 control을 소비자에게 넘긴다. 그 지점으로 다시 던져지는 에러(`it.throw(err)` 등)는 소비자 쪽 문제인데, catch가 그것까지 "이 item이 이 단계에서 실패"로 세어 버린다. 실패가 엉뚱한 item과 단계에 붙고, 진짜 에러는 삼켜진다.

```ts
// 나쁨
try {
  yield fn(item);   // fn 실패와 소비자 쪽 실패가 구분되지 않는다
} catch (err) { summary.failed++; }
```

**Takeaway:** try는 "이 item의 이 단계"만 감싼다. 그래야 실패가 정확한 item과 단계로 추적된다.

---

## 18. 성공한 span 버리기: SpanProcessor filter

Item마다 span을 만들면 성공한 수천 개 span이 저장 비용만 먹는다. Export 전에 SpanProcessor에서 실패한 span과 그 조상만 남기고 나머지는 버린다.

```ts
import type { Context } from "@opentelemetry/api";
import { SpanStatusCode } from "@opentelemetry/api";
import type { ReadableSpan, Span, SpanProcessor } from "@opentelemetry/sdk-trace-base";

class KeepFailuresProcessor implements SpanProcessor {
  private keep = new Set<string>(); // 실패한 자식이 있는 span id

  constructor(private next: SpanProcessor) {}

  onStart(span: Span, ctx: Context) {
    this.next.onStart(span, ctx);
  }

  onEnd(span: ReadableSpan) {
    const id = span.spanContext().spanId;
    const parentId = span.parentSpanContext?.spanId; // SDK 버전에 따라 parentSpanId
    const failed = span.status.code === SpanStatusCode.ERROR;
    const isRoot = !parentId;

    if (failed || this.keep.has(id)) {
      if (parentId) this.keep.add(parentId); // 부모도 남기도록 표시
    }
    if (failed || isRoot || this.keep.has(id)) {
      this.keep.delete(id);
      this.next.onEnd(span); // export
    }
    // 그 외: 성공한 leaf span은 버린다
  }

  forceFlush() { return this.next.forceFlush(); }
  shutdown() { return this.next.shutdown(); }
}
```

이게 동작하는 이유: **자식 span은 부모보다 먼저 끝난다.** 그래서 자식의 `onEnd`에서 "부모를 남겨라"를 표시하면, 나중에 부모의 `onEnd`가 그 표시를 볼 수 있다. 순서가 반대였다면 부모는 이미 버려진 뒤라서 Tempo에 부모 없는 자식(broken waterfall)이 남는다.

```text
item.transform  (ERROR) 끝남  → keep에 부모 id 추가, export
item.process            끝남  → keep에 있음 → export
etl.run         (root)  끝남  → export
```

```traceql
{ resource.service.name = "etl-job" && status = error }
```

**Takeaway:** 실패와 그 경로만 남긴다. 이 방식은 children-before-parent 순서에 기대고 있다.

---

## 19. Signal 처리, flush, exit code

### 19.1 SIGTERM / SIGINT

Kubernetes는 pod을 멈출 때 SIGTERM을 보내고, `terminationGracePeriodSeconds`(기본 30초) 뒤에 SIGKILL을 보낸다. 로컬에서 Ctrl+C는 SIGINT다. Signal을 받으면 run 결과를 record하고, telemetry를 flush하고, 그 다음 exit한다.

```ts
const FLUSH_TIMEOUT_MS = 5_000; // grace period보다 훨씬 짧게

function withTimeout(p: Promise<unknown>, ms: number) {
  return Promise.race([p, new Promise((r) => setTimeout(r, ms))]);
}

function onSignal(signal: "SIGTERM" | "SIGINT", code: number) {
  process.once(signal, async () => {
    logger.warn({ signal }, "etl_run_interrupted");
    finishRun(summary, "failure");                       // record
    await withTimeout(sdk.shutdown(), FLUSH_TIMEOUT_MS); // flush (최대 5초)
    process.exit(code);                                  // 그 다음 exit
  });
}

onSignal("SIGTERM", 143);
onSignal("SIGINT", 130);
```

왜 flush가 exit보다 먼저인가: exporter는 span과 metric을 메모리에 모았다가 비동기로 보낸다. `process.exit()`는 대기 중인 Promise를 기다리지 않고 바로 프로세스를 끝낸다. Flush 없이 exit하면 바로 그 실패 run의 telemetry가 사라진다.

왜 timeout cap이 필요한가: collector가 응답하지 않으면 flush가 영원히 걸린다. 그러면 SIGKILL을 맞고 exit code도 log도 남지 않는다.

### 19.2 Exit code 128 + N

Signal로 끝난 프로세스는 관례상 `128 + signal 번호`로 exit한다.

```text
SIGINT  = 2   → 128 + 2  = 130
SIGTERM = 15  → 128 + 15 = 143
SIGKILL = 9   → 128 + 9  = 137  (OOMKilled에서도 보이는 값)
```

### 19.3 Kubernetes Job semantics

```text
exit 0       → pod Succeeded → Job complete
exit != 0    → pod Failed    → backoffLimit까지 재시도, 그 뒤 Job Failed
```

```bash
kubectl get pod -l job-name=<job> -o jsonpath='{.items[*].status.containerStatuses[*].state.terminated.exitCode}'
```

**Takeaway:** signal → record → flush(상한 있음) → `128+N`으로 exit. 순서를 바꾸면 telemetry가 사라진다.

---

## 20. Circular import (config ↔ logger)

`config.ts`는 잘못된 설정을 경고하려고 logger를 import한다. `logger.ts`는 log level을 읽으려고 config를 import한다. 그러면 둘 중 하나는 import 시점에 상대 module이 아직 초기화되지 않은 상태를 본다.

```ts
// config.ts
import { logger } from "./logger";
export const config = { logLevel: process.env.LOG_LEVEL ?? "info" };
if (!process.env.SEARCH_URL) logger.warn("SEARCH_URL missing");

// logger.ts
import pino from "pino";
import { config } from "./config";
export const logger = pino({ level: config.logLevel });
// config.ts가 먼저 로드되면: logger.ts 평가 시점에 config는 아직 초기화 전
// → ESM: ReferenceError(TDZ), CJS: undefined
```

해결: 둘 다 필요로 하는 값을 아무것도 import하지 않는 util 파일로 뺀다.

```ts
// env.ts — import 없음. 그래서 cycle에 낄 수 없다.
export function env(name: string, fallback?: string) {
  return process.env[name] ?? fallback;
}

// logger.ts
import pino from "pino";
import { env } from "./env";
export const logger = pino({ level: env("LOG_LEVEL", "info") });

// config.ts
import { env } from "./env";
import { logger } from "./logger";
export const config = { searchUrl: env("SEARCH_URL") };
if (!config.searchUrl) logger.warn("SEARCH_URL missing");
```

```text
config ──▶ logger ──▶ env
   └────────────────▶ env
(cycle 없음)
```

**Takeaway:** dependency가 없는 leaf 파일은 cycle에 낄 수 없다. 공유 값은 거기로 내린다.

---

## 21. Layered architecture: import는 아래로만

각 layer는 자기보다 아래 layer만 import한다. 위쪽을 import하면 cycle과 숨은 결합이 생긴다.

```text
┌──────────────────────────┐
│ cli / entrypoint         │  run 시작, signal, exit code
├──────────────────────────┤
│ pipeline                 │  fetch → transform → load 순서
├──────────────────────────┤
│ sources / search client  │  CMS, product API, 검색 엔진
├──────────────────────────┤
│ telemetry / logger       │  span, metric, log
├──────────────────────────┤
│ util (env, chunk ...)    │  dependency 없음
└──────────────────────────┘
        import 방향: 위 → 아래
```

```ts
// src/pipeline/run.ts — OK: 아래 layer를 import
import { fetchPage } from "../sources/cms";
import { withChildSpan } from "../telemetry/run";

// src/telemetry/run.ts — 나쁨: 위 layer를 import
// import { runPipeline } from "../pipeline/run";
```

예: item failure helper(count + log)는 그것을 세는 telemetry layer에 둔다. Pipeline은 그 helper를 호출만 한다.

**Takeaway:** "이 파일이 이걸 import해도 되나?"는 layer 그림 하나로 답이 나온다.

---

## 22. 코드를 지우기 전의 evidence rule

"이거 필요 없어 보인다"로 지우지 않는다. 세 가지를 답할 수 있어야 한다.

```text
1. 왜 추가됐나?          (commit, PR, issue)
2. 무엇을 지키고 있나?    (어떤 실패를 막는가, 어떤 test가 보호하는가)
3. 왜 그 이유가 더 이상 성립하지 않나?  (예: 플랫폼이 이제 보장한다)
```

```bash
git log -S "aliasPointsAt" --oneline -- src/   # 언제, 왜 들어왔나
git blame -L 40,60 src/search/swap.ts
```

Mutation check: 보호 장치를 일부러 깨고 test가 빨개지는지 본다. 빨개지지 않으면 그 test는 아무것도 지키지 않는다.

```ts
// swap.ts 에서 read-back을 잠깐 지운다
// if (await aliasPointsAt(alias, next)) return;
```

```bash
npm test -- swap   # 실패해야 정상. 통과하면 test가 그 동작을 안 본다.
git checkout -- src/search/swap.ts   # 원복
```

예: alias read-back은 "응답을 잃은 성공"이라는 실제 실패를 막는다. 그래서 남겼다. 반면 age threshold는 "동시 run" 때문이었는데, `Forbid`가 그 전제를 없앴다. 그래서 지웠다.

**Takeaway:** 지울 때도 추가할 때처럼 근거가 필요하다. 근거는 "왜 추가 → 무엇을 지킴 → 왜 더 이상 아님" 세 단계다.

---

## 23. OTel 환경 변수: chart vs local `.env`

OpenTelemetry SDK는 코드 대신 표준 환경 변수로 설정할 수 있다.

| 변수 | 의미 | 예 |
|---|---|---|
| `OTEL_EXPORTER_OTLP_ENDPOINT` | trace/metric을 보낼 collector | `http://otel-collector.observability:4318` |
| `OTEL_EXPORTER_OTLP_PROTOCOL` | 전송 방식 | `http/protobuf` |
| `OTEL_SERVICE_NAME` | Tempo/Prometheus에서 보일 서비스 이름 | `etl-job` |
| `OTEL_RESOURCE_ATTRIBUTES` | 모든 signal에 붙는 공통 label | `deployment.environment=prod,brand=brand-a` |
| `OTEL_TRACES_SAMPLER` | 어떤 trace를 남길지 | `parentbased_always_on` |
| `OTEL_METRIC_EXPORT_INTERVAL` | metric push 주기(ms) | `60000` |
| `OTEL_EXPORTER_OTLP_TIMEOUT` | export 한 번의 timeout(ms) | `10000` |

어디서 오는가:

```yaml
# deployment chart (values.yaml) — 환경마다 다른 값. 운영의 진실은 여기.
env:
  - name: OTEL_EXPORTER_OTLP_ENDPOINT
    value: http://otel-collector.observability:4318
  - name: OTEL_SERVICE_NAME
    value: etl-job
  - name: OTEL_RESOURCE_ATTRIBUTES
    value: deployment.environment=prod,brand=brand-a
```

```bash
# local .env — 내 머신에서만. collector가 없으면 endpoint를 비워 둔다.
OTEL_SERVICE_NAME=etl-job-local
OTEL_EXPORTER_OTLP_ENDPOINT=http://localhost:4318   # docker로 띄운 collector
OTEL_METRIC_EXPORT_INTERVAL=5000                     # 로컬에서 빨리 보려고
```

```ts
// 코드는 env를 읽기만 한다. 값을 하드코딩하지 않는다.
import { NodeSDK } from "@opentelemetry/sdk-node";
const sdk = new NodeSDK(); // OTEL_* 를 SDK가 스스로 읽는다
sdk.start();
```

주의: batch job은 export interval이 오기 전에 끝날 수 있다. 그래서 끝에서 `sdk.shutdown()`(flush 포함)이 반드시 필요하다(19장).

**Takeaway:** 환경마다 다른 값은 chart, 내 머신 값은 `.env`, 코드에는 둘 다 없다.

---

## 24. In-cluster collector vs cluster 밖 서비스

`http://otel-collector.observability:4318` 같은 이름은 Kubernetes 내부 DNS다. Cluster 안의 pod만 resolve할 수 있다. Cluster 밖에서 도는 서비스(다른 cloud의 job, 로컬 머신)는 이 이름을 찾지 못하고, 찾더라도 network가 막혀 있다.

```text
cluster 안의 pod   → otel-collector.observability:4318   OK
cluster 밖 서비스   → otel-collector.observability:4318   DNS 실패 / 접근 불가
```

Cluster 밖에서 보내려면 platform team이 collector를 밖으로 노출해야 한다.

```text
- public(또는 VPN 내부) endpoint: 예) https://otel.example.internal
- TLS
- 인증(token/header) — 값은 secret으로 주입, 코드나 repo에 두지 않음
- 받는 signal 범위(traces, metrics, logs)
```

```bash
OTEL_EXPORTER_OTLP_ENDPOINT=https://otel.example.internal
OTEL_EXPORTER_OTLP_HEADERS=authorization=Bearer%20${OTEL_TOKEN}   # token은 secret에서
```

Log 경로도 다르다.

```text
cluster 안: stdout → node의 log agent가 수집 → Loki   (앱은 stdout만 쓰면 됨)
cluster 밖: stdout을 수집해 줄 agent가 없음
           → OTLP logs나 Loki push API로 직접 보내야 함
```

**Takeaway:** 내부 DNS 이름은 cluster 밖에서 의미가 없다. 밖에서 쓰려면 노출된 endpoint, TLS, 인증, log 경로를 platform team과 따로 정해야 한다.

---

## 25. Git: commit별 검증, hunk 분리, merge vs rebase

### 25.1 Commit마다 검증하기 (`.env` 없는 clean worktree)

내 작업 디렉터리에는 `.env`와 build 결과물이 있어서 "내 머신에서만 되는" commit을 놓친다. 별도 worktree에서 commit 하나씩 checkout해 typecheck/test를 돌린다. 이 worktree에는 `.env`가 없으므로 test가 `.env`에 기대면 바로 드러난다.

```bash
git worktree add /tmp/verify HEAD
for c in $(git rev-list --reverse main..HEAD); do
  git -C /tmp/verify checkout -q "$c"
  (cd /tmp/verify && npm ci --silent && npm run typecheck && npm test) \
    || echo "FAIL at $c"
done
git worktree remove /tmp/verify
```

### 25.2 Hunk 단위로 commit 나누기

한 파일 안에 refactor와 behaviour change가 섞이면 hunk 단위로 나눠 stage한다.

```bash
git add -p src/telemetry/run.ts   # y/n/s(split)로 hunk 선택
git commit -m "refactor: move item failure helpers into telemetry"
git add -p src/telemetry/run.ts
git commit -m "feat(logs): log a failed fetch as etl_fetch_failed"
```

### 25.3 Push된 branch에는 rebase 대신 merge

이미 push한 branch를 rebase하면 history가 바뀌어 force-push가 필요하다. 리뷰어의 comment 위치와 다른 사람의 checkout이 깨진다. Main을 따라잡을 때는 merge한다.

```bash
git fetch origin
git merge origin/main      # 새 merge commit, force-push 불필요
git push
```

**Takeaway:** commit 하나 = 의도 하나, 각 commit은 혼자서 build/test가 통과해야 한다. Push된 history는 다시 쓰지 않는다.
