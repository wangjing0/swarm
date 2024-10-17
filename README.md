# OpenAI's swarm agents with memory 

## install
```python
pip install git+https://github.com/jingwang0/swarm.git
```

## example handoff agent
```python
from examples.triage_agent.agents import triage_agent
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

## example triage agent
```python
from swarm import Swarm
from swarm.repl import process_and_print_streaming_response, pretty_print_messages
from examples.triage_agent.agents import triage_agent

client = Swarm()

messages = []
stream = True
debug = False

while True:
    user_input = input("\033[90mUser\033[0m: ")
    messages.append({"role": "user", "content": user_input})

    response = client.run(
        agent=triage_agent,
        messages=messages,
        context_variables={},
        stream=stream,
        debug=debug,
    )

    if stream:
        response = process_and_print_streaming_response(response)
    else:
        pretty_print_messages(response.messages)

    messages.extend(response.messages)
    agent = response.agent

memory = client.memory.model_dump()['memory']
print("number of turns:", len(memory))
```
```
number of turns: 17
```

```python
memory[-1]
```

```
{'messages': [{'role': 'user', 'content': 'bring me back '},
  {'content': None,
   'role': 'assistant',
   'function_call': None,
   'tool_calls': [{'id': 'call_Hi7K1Pxpbi1Z3saNF8gaev5U',
     'function': {'arguments': '{}', 'name': 'transfer_back_to_triage'},
     'type': 'function'}],
   'refusal': None,
   'sender': 'Refunds Agent'},
  {'role': 'tool',
   'tool_call_id': 'call_Hi7K1Pxpbi1Z3saNF8gaev5U',
   'tool_name': 'transfer_back_to_triage',
   'content': '{"assistant": "Triage Agent"}'},
  {'content': "You're back with me now! How can I assist you further?",
   'role': 'assistant',
   'function_call': None,
   'tool_calls': None,
   'refusal': None,
   'sender': 'Triage Agent'}],
 'agent': {'name': 'Triage Agent',
  'model': 'gpt-4o',
  'instructions': "Determine which agent is best suited to handle the user's request, and transfer the conversation to that agent.",
  'functions': [<function examples.triage_agent.agents.transfer_to_sales()>,
   <function examples.triage_agent.agents.transfer_to_refunds()>],
  'tool_choice': None,
  'parallel_tool_calls': True},
 'context_variables': {},
 'timestamp': '2024-10-17 13:33:23'}
```