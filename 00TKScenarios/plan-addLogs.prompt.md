## Plan: Stałe logowanie Q/A do Application Insights

Dodamy jeden spójny event loga emitowany zawsze dla `/ask`, `/chat` oraz po zakończeniu `/chat/stream` (w `finally`). Log będzie zawierał pełne pytanie i pełną odpowiedź (tekst), a multimodal będziemy ignorować (gdy `content` nie jest `str`). Przy ustawionym `APPLICATIONINSIGHTS_CONNECTION_STRING` logi trafią do Application Insights dzięki już istniejącemu `configure_azure_monitor(...)`.

### Steps
1. Dodać helper logowania w `app/backend/telemetry_qa.py`: `extract_last_user_text(messages)`, `extract_answer_text(result)`, `log_qa_event(...)`.
2. Ustalić stały event `event="qa_log"` i pola: `route`, `session_state`, `user_oid`, `question`, `answer`, `is_stream`, `skipped_reason`.
3. Wpiąć `log_qa_event(...)` po `approach.run(...)` w `ask()` i `chat()` w `app/backend/app.py`.
4. W `chat_stream()` w `app/backend/app.py` owinąć generator NDJSON: akumulować `delta.content` (tylko `str`) i w `finally` wysłać `qa_log`.
5. Dodać testy `caplog` w `tests/test_app.py`: `/ask`, `/chat`, `/chat/stream` + przypadek multimodal → `skipped_reason`.

### Further Considerations
1. Jeśli `APPLICATIONINSIGHTS_CONNECTION_STRING` nie jest ustawione, log będzie tylko w logach aplikacji (nie w App Insights).
