---

title: "python"
keywords: homepage
sidebar: mydoc_sidebar
permalink: python.html

---

# Python 3

### [WSGI vs ASGI](https://kangbk0120.github.io/articles/2022-02/cgi-wcgi-asgi)
- CGI(Common Gateway Interface) : When Client requested for data, server has to send the data back to Web Application. CGI is a standard/regulation for the language differences. CGI has to restart the application process everytime there's a new request, and this tend to be a huge burden for script language since it takes more time to execute. **This is the reason for WSGI came out (only for python)**
- WSGI(Web Server Gateway Interface): Django and Flask are not web servers, and web servers don't support Python. Therefore, there has to be a "interpreter" between those two, and that is WSGI. But it is syncronized, which made it quite slow and hard to comprehensive many data. gunicorn is often used.
- ASGI(Asynchronous Server Gateway Interface) : FastAPI with Uvicorn succeed to make the gateway interface process asynchronous, solving problems of WSGI.
> Django, Flask, FastAPI are web framework, and gunicorn, uvicorn are web server.

### Django vs Flask vs FastAPI
- Django has a lot of third party apps but it's way heavier.
- Flask is for simple applications but it could be more difficult when you add more third party apps.
- FastAPI is very fast and easy to code. However the quantity of third party apps is quite low.

### Managing Memory
- references counting: 얼마나 쓰고있는지 확인
- cyclic garbage collector: A와 B가 서로를 refer하고 있을때 이것을 감지하는 역할.
    - Explicitly: `collect` method in `gc` module
- references counting 이 0 가 되면 deallocate한다.
- `del` 로 explicit deallocation 가능

### decorator
- it wraps another function or class. (extending)
- Multiple decorators, or adding parameters are also fine.
- It seems very similar concept to **currying**.

```python
def my_decorator(func):
    def wrapper():
        print("Before function call")
        func()
        print("After function call")
    return wrapper

@my_decorator
def say_hello():
    print("Hello!")

# Calling the function
say_hello()
```

- advanced:
```python
def repeat(num_times):
    def decorator_repeat(func):
        def wrapper(*args, **kwargs):
            for _ in range(num_times):
                result = func(*args, **kwargs)
            return result
        return wrapper
    return decorator_repeat

@repeat(3)
def greet(name):
    print(f"Hello {name}")

greet("Alice")  # prints "Hello Alice" three times
```

### **GIL(Global Interpreter Lock) - using CPython**
- Enables to have multithread by synchronizing threads to be executed only one native thread per bytecode. → due to this, python cannot achieve true parallel using GIL.
- This **LOCK** is necessary because **CPython's memory management is not thread-safe**. (CPython? interpreter)
- The GIL can be a significant bottleneck for multi-threaded programs because only one thread can execute at a time, even on multi-core processors. This makes CPU-bound programs written in Python difficult to scale across multiple cores or processors. For CPU-bound tasks, Python's **`multiprocessing`** module can be used to take full advantage of multiple cores, as it sidesteps the GIL by using subprocesses instead of threads.
- HOW TO MAKE IT TRUE PARALLEL?
    - Multiple Core → multiprocessing module
    - asyncio module
    - Numba module
    - PyPy interpreter
- Error handle in python:
    - try: exception.

    ```python
    try:
        x = 1 / 0
    except ZeroDivisionError as e:
        print(f"An error occurred: {e}")
    finally:
        print("This will run no matter what.")
    ```

- `is` vs `==`
    - is: identity(in memory, same reference) vs ==: same value
- @Static method vs @Class method
    - static method의 경우 부모 클래스의 클래스 속성 값을 가져오지만 class method의 경우 cls인자를 활용하여 현재 클래스의 클래스 속성을 가져온다.
