# OpenAI's swarm agents with memory 

## install
```python
pip install git+https://github.com/jingwang0/swarm.git
```

## example
```python
from swarm import Swarm, Agent

client = Swarm()

def transfer_to_agent_b():
    return agent_b


agent_a = Agent(
    name="Agent A",
    instructions="You are a helpful agent.",
    functions=[transfer_to_agent_b],
)

agent_b = Agent(
    name="Agent B",
    instructions="Only speak in Chinese.",
)

response = client.run(
    agent=agent_a,
    messages=[{"role": "user", "content": "I want to talk to agent B."}],
)

print(response.messages[-1]["content"])

print(client.memory.model_dump())
```

```
正在将您转接到B代理。
```

```
{'memory': [{'messages': [{'content': None,
     'role': 'assistant',
     'function_call': None,
     'tool_calls': [{'id': 'call_vJvmKrQZU2v7FVLgbX3TVz9b',
       'function': {'arguments': '{}', 'name': 'transfer_to_agent_b'},
       'type': 'function'}],
     'refusal': None,
     'sender': 'Agent A'},
    {'role': 'tool',
     'tool_call_id': 'call_vJvmKrQZU2v7FVLgbX3TVz9b',
     'tool_name': 'transfer_to_agent_b',
     'content': '{"assistant": "Agent B"}'},
    {'content': '正在将您转接到B代理。',
     'role': 'assistant',
     'function_call': None,
     'tool_calls': None,
     'refusal': None,
     'sender': 'Agent B'}],
   'agent': {'name': 'Agent B',
    'model': 'gpt-4o',
    'instructions': 'Only speak in Chinese.',
    'functions': [],
    'tool_choice': None,
    'parallel_tool_calls': True},
   'context_variables': {},
   'timestamp': '2024-10-13 23:07:21'}]}
```
