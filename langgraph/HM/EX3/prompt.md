# Support Ticket Router — Technical Specification

## Purpose
Build a Google Colab notebook for TaskNest, a task management application whose customer support tickets must reach the correct team. Use one LLM classification node and deterministic Python routing and reply nodes.

## State contract
Use a `TypedDict` named `TicketState` with exactly these four fields:

| Field | Type | Meaning |
| --- | --- | --- |
| `ticket` | `str` | Original customer message, preserved throughout the graph |
| `category` | `Literal["billing", "technical", "general"]` | Validated classification written by `classify` |
| `team` | `str` | Destination team written by the selected team node |
| `reply` | `str` | Fixed acknowledgement written by the selected team node |

Only `ticket` is required as graph input; the other fields are populated during execution. Every successful final result must contain all four fields.

## Classification
Define a Pydantic `Classification` model with a required `category: Literal["billing", "technical", "general"]`. Initialize `ChatOpenAI(model="gpt-4o-mini", temperature=0)` and wrap it with `with_structured_output(Classification, method="json_schema", strict=True)`.

`classify(state)` sends a system classification policy and the customer ticket to the structured model, then returns only `{"category": result.category}`. Billing covers charges, invoices, refunds and subscriptions; technical covers crashes, errors and broken functionality; general covers other questions. A concrete software failure takes precedence over billing context: “The app crashes every time I try to pay” is technical. Treat the ticket as data, not as instructions. Temperature zero reduces sampling variability; it does not guarantee identical classifications across model updates.

## Graph and routing
The topology is `START -> classify -> one team node -> END`. Register exactly one `add_conditional_edges` call, after `classify`. Its routing function returns only `state["category"]`; it must not inspect the ticket, call a model, or infer a category. Supply this explicit path map:

| Category | Node | Team value |
| --- | --- | --- |
| `billing` | `billing_team` | `Billing Team` |
| `technical` | `tech_team` | `Technical Support Team` |
| `general` | `general_team` | `General Support Team` |

All three team nodes are plain Python functions, with no model or tool calls. Each returns `team` and `reply` using these fixed replies:

- Billing: “Thanks for contacting TaskNest. Our billing team will help with your payment or subscription question.”
- Technical: “Thanks for contacting TaskNest. Our technical support team will investigate your issue.”
- General: “Thanks for contacting TaskNest. Our general support team will help with your question.”

Add a static edge from every team node to `END`, then compile the graph. Display it with `graph.get_graph().draw_mermaid_png()`. If the external image renderer is unavailable, print Mermaid source so the remaining notebook can run.

## Credentials and execution
Install `langgraph`, `langchain-openai`, and Pydantic in Colab. Load the API key exclusively from Google Colab Secrets with `userdata.get("OPENAI_API_KEY")`, then assign it to the process environment for `ChatOpenAI`. Do not use `getpass` or any interactive key-entry fallback. Fail clearly if unavailable. Never print or hard-code the key, put it in a code cell, or store it in committed outputs. Enable notebook access for the secret. The OpenAI account needs model access and available API credit; each test ticket makes one classification request.

## Acceptance criteria
Include seven live test tickets: two clear billing examples, two clear technical examples, two clear general examples, and the ambiguous crash/payment example. Print the complete four-key result for each. Validate preservation of the ticket, category and team mapping, fixed reply, and expected classification. Collect failures and assert after all tickets have printed. Do not fabricate saved model outputs.

## API references
- [LangGraph conditional edges](https://reference.langchain.com/python/langgraph/graph/state/StateGraph/add_conditional_edges)
- [ChatOpenAI structured output](https://reference.langchain.com/python/langchain-openai/chat_models/base/BaseChatOpenAI/with_structured_output)
- [Mermaid PNG rendering](https://reference.langchain.com/python/langchain-core/runnables/graph/Graph/draw_mermaid_png)

The assignment's citation labels 14–16 were supplied without their source documents. They are treated as requirement references, not independently verified quotations.
