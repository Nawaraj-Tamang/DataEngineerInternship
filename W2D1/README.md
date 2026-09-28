# Data Engineer Internship — Week 2, Day 1

## Tasks Completed

- Set up a PostgreSQL connection notebook (`postgres_connection.ipynb`) using `psycopg2` and `python-dotenv`
- Wrote a credential-loading cell that reads `DB_HOST`, `DB_PORT`, `DB_NAME`, `DB_USER`, `DB_PASSWORD` from a `.env` file and fails loudly (`RuntimeError`) if any are missing
- Debugged a `NameError: name 'load_dotenv' is not defined` caused by running a cell before its import cell had executed
- Debugged a `.env` not being found/loaded correctly — traced it to `find_dotenv()` matching the *nearest* `.env` file when walking up from the notebook, rather than the one intended
- Switched from a bare `load_dotenv(override=True)` to `find_dotenv(raise_error_if_not_found=True)` so the resolved path is always printed on success or failure, making this class of bug visible immediately instead of silently loading zero variables
- Decided to keep `.env` local to each week/day folder (`W2D1/.env`) rather than a single project-root `.env`
- Built a hardened `DBConnection` class (`reconnect()`, `cursor()`, `commit()`, `rollback()`, `close()`) plus a `with_retry` decorator that retries a db-operation function up to 3 times on `psycopg2.OperationalError`, rolling back or reconnecting between attempts
- Created `requirements.txt` (`psycopg2-binary`, `python-dotenv`)
- Wrote DDL for two related tables — `teacher` and `student` (one-to-many via `student.teacher_id → teacher.teacher_id`) — with `CREATE TABLE IF NOT EXISTS`
- Debugged an `InvalidForeignKey` error caused by a stale `teacher` table (created in an earlier, incomplete run) lacking a primary key, which meant `IF NOT EXISTS` silently skipped recreating it; fixed by dropping both tables with `CASCADE` and re-running the schema cell
- Seeded sample rows into `teacher` and `student`, then verified with a join query and row-count check

## How to Run

1. Activate the virtual environment:
   ```bash
   source venv/bin/activate
   ```
2. Install dependencies:
   ```bash
   pip install -r requirements.txt
   ```
3. Create a `.env` file in `W2D1/` with:
   ```
   DB_HOST=localhost
   DB_PORT=5432
   DB_NAME=your_db_name
   DB_USER=your_db_user
   DB_PASSWORD=your_db_password
   ```
4. Open `postgres_connection.ipynb` (or `teacher_student.ipynb`) in VS Code.
5. Select the `venv` interpreter as the notebook kernel.
6. Run the cells in order:
   - Logging setup
   - Load credentials from `.env`
   - Build `DBConnection` + `with_retry`, connect
   - Create schema (`teacher`, `student`)
   - Seed sample data
   - Verify with a join query
   - Close the connection

## What I Learned

Learned that `find_dotenv()` walks *upward* from the notebook's directory and stops at the **first** `.env` it finds — so an empty or stray `.env` in a nested folder silently shadows the intended one, and `load_dotenv()` gives no error in that case, just zero variables loaded. Printing the resolved `dotenv_path` on every load turned an invisible failure into an obvious one. Also learned that `CREATE TABLE IF NOT EXISTS` is not a migration tool: if a table already exists in a broken or incomplete shape (e.g., missing its primary key) from an earlier failed run, Postgres skips recreating it as-is, and any table trying to reference it with a foreign key will fail with `InvalidForeignKey` until the stale table is explicitly dropped. On the connection-handling side, learned why a retry decorator distinguishes between a *dead transaction* (fixed with `rollback()`) and a *dead connection* (needs `reconnect()`) — treating every failure the same way would leave the connection unusable after the first real drop.

## Challenges

The credentials error was the most time-consuming part precisely because it didn't look like a credentials problem — the file was found, no exception was raised on `load_dotenv()` itself, and the only symptom was *all five* variables reporting missing at once, which took a `repr()` dump of the file contents and a check of `find_dotenv()`'s resolved path to properly diagnose (empty file due to a shadowing nested `.env`, not a typo or encoding issue). The `InvalidForeignKey` error was similarly indirect — the actual problem (a stale, incorrectly-shaped `teacher` table left over from an earlier attempt) wasn't obvious from the traceback alone and required inspecting `information_schema.columns` to confirm before it made sense to drop and recreate.
