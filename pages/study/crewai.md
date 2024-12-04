---
title: crewAI
last_updated: Dec 4, 2024
keywords: study
sidebar: mydoc_sidebar
comments: true
permalink: crewai.html

---

## [crewAI](https://docs.crewai.com/introduction)
- Very easy and fast way to create AI agents.
- You can create multiple agents in one project.
- You can give it memory - but if you might need vector DB to store the memory, and embedding model to create the vector. Check [here](https://docs.crewai.com/concepts/memory) for more details.
```python
from crewai import Crew, Agent, Task, Process

# short term
my_crew = Crew(
    agents=[...],
    tasks=[...],
    process=Process.sequential,
    memory=True,
    verbose=True
)

from crewai import Crew, Agent, Task, Process

# Assemble your crew with memory capabilities
my_crew = Crew(
    agents=[...],
    tasks=[...],
    process="Process.sequential",
    memory=True,
    long_term_memory=EnhanceLongTermMemory(
        storage=LTMSQLiteStorage(
            db_path="/my_data_dir/my_crew1/long_term_memory_storage.db"
        )
    ),
    short_term_memory=EnhanceShortTermMemory(
        storage=CustomRAGStorage(
            crew_name="my_crew",
            storage_type="short_term",
            data_dir="//my_data_dir",
            model=embedder["model"],
            dimension=embedder["dimension"],
        ),
    ),
    entity_memory=EnhanceEntityMemory(
        storage=CustomRAGStorage(
            crew_name="my_crew",
            storage_type="entities",
            data_dir="//my_data_dir",
            model=embedder["model"],
            dimension=embedder["dimension"],
        ),
    ),
    verbose=True,
)

```

```sh
	# 1. Make virtual env - and start
	virtualenv venv
	sourch venv/bin/activate

	# 2. get crew AI
	pip install crewai
	pip install 'crewai[tools]'

	# 3. create demo
	crewai create crew demo

	# 4. rust is necessary:
	curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh

	# 4.1 check rust
	rustc --version

	# 5. other dependancy install
	# crewai install only works with python up to 3.12 at the moment.
	# You can use pyenv to get the different python version per project
	crewai install

	# 6. Run
	crewai run
```

Some context:
1. By using `process=Process.sequential`, it automatically passes the previous task result to the next task. This can be configured differently, to only send require parameters. The sequence is set by `tasks.yml`
