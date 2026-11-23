# Curious Minds Club Chatbot

Curious Minds is an after-school enrichment chatbot for teenagers. It uses LangGraph and Gradio to discuss five clubs: Astronomy, Cooking, Football, World Geography, and Python Programming. The assistant is instructed to answer only questions about the selected club and suggest switching clubs for off-topic questions. This restriction depends on the model following its instruction.

## Architecture

- **Graph:** A single `chatbot` node connects `START` to `END` and calls the configured model once for each user message. `MODEL_NAME` is defined once in the notebook.
- **State:** `messages` uses the `add_messages` reducer to retain conversation history; `topic` has no reducer, so a new value replaces the previous one.
- **System message:** On every call the node builds a `SystemMessage` using the current topic, prepends it to the history, and does not save it to the state. The system message also receives the selected club’s scope and boundary text.
- **Persistence:** A Supabase PostgreSQL `PostgresSaver` stores checkpoints under thread IDs. Saved conversations restore their messages and active topic across runtime restarts. The menu lists up to 50 recent candidate conversations and filters empty and test threads.
- **Interface:** Gradio provides a club selector, chat, message input, saved-conversation selector, refresh button, and a new-conversation button.

## Files

- `curious_minds.ipynb` — self-contained setup, graph, verification, and interface.
- `prompt.md` — original specification and revision history.

## Secrets and database setup

Create a Supabase project and obtain its PostgreSQL connection URL. You also need an OpenAI API key with access to the model named in the notebook. In Google Colab, open **Secrets** (the key icon), add these names, and enable notebook access:

| Secret | Value |
| --- | --- |
| `SUPABASE_DB_URL` | Supabase PostgreSQL connection URL, including database credentials. |
| `OPENAI_API_KEY` | Your OpenAI API key. |

The notebook reads Colab Secrets using `userdata.get`, falling back to environment variables when a secret is unavailable or outside Colab. It sets `OPENAI_API_KEY` in the runtime environment for the model client and passes the database URL to psycopg. Never commit credentials or put their values in the notebook.

The notebook connects with `autocommit=True`, `row_factory=dict_row`, and `prepare_threshold=0`, creates a `PostgresSaver`, and calls `setup()` to initialize its tables. Use an account permitted to create and access those tables. Saved conversations require the same database to remain available.

## GitHub and Colab workflow

1. Commit and push the project files to GitHub, without credentials or local dependency caches.
2. In Google Colab, select **File → Open notebook → GitHub** and open `curious_minds.ipynb` from your repository. Alternatively, use **Upload** to open a local copy. No neighboring Python file is needed.
3. Add and enable the two Colab Secrets, then run the cells in order. The notebook installs dependencies, initializes the checkpointer and graph, draws the graph, runs the standalone verification, and starts Gradio. The verification uses the persistent `test-1` thread; a fresh thread has eight messages after the test, and rerunning adds eight more. Inspect the answers for topic adherence.
4. Open the Gradio link, choose a club, and send a message. Use **Saved conversations** to restore a checkpoint or **New conversation** for a fresh thread. After a runtime restart, rerun the notebook to reconnect to the same Supabase database.

The Gradio link is public and uses your OpenAI key. Do not share it; stop the launch cell when finished.


## Testing

The notebook's original four-turn check uses `test-1`. The additional **Guard tests** exercise role claims, prompt injection, topic claims, mixed questions, and club-switch follow-ups. Message counts and saved topics are asserted; flags are only hints. Read every answer and record incorrect ones for reflection.

**Guard tests II** checks ten club-boundary cases with three repetitions each (`REPEATS = 3`) on fresh `test-guard2-` threads hidden from the saved list. Its flags are heuristics, including a `keyword` check for partial answers; a human must review every answer. Case 1 expects an answer because food belongs to the Cooking club.

The **Supabase inspection** cell uses read-only queries to show table columns and counts, checkpoint metadata, and blob channel metadata without displaying blob contents. The **UI logic test** calls the interface's backend functions without opening a browser; it creates a real conversation that appears in the saved list. Threads beginning with `test-` are hidden from that menu and remain in the database unless `CLEANUP_TEST_THREADS` is set to `True` in the guard cell (which deletes the guard threads it created).

To check persistence after a restart:

1. Run the notebook once and chat in the UI.
2. Select **Runtime → Restart session**.
3. Rerun the install, secrets, configuration, checkpointer, graph, and UI cells, but not the test cells.
4. Run the **Persistence check** cell to read saved labels and the latest conversation from Supabase.
5. Launch the UI, open an old conversation, verify its messages and club, and send another message in the same thread.

Complete this manual browser checklist as well:

- [ ] Ask about pancakes in Cooking.
- [ ] Ask the football-coach question in Cooking and in Football.
- [ ] Ask a mixed on-topic and off-topic question.
- [ ] Switch clubs in one conversation and ask about the old topic.
- [ ] Verify New conversation clears the chat and resets the club.
- [ ] Verify the saved list shows `[Club] first words`.
- [ ] Use Refresh.
- [ ] Open an old conversation after the restart.
