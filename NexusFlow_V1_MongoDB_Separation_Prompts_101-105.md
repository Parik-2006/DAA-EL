# DAA-EL V1 MongoDB Separation — Prompts 101–105

## Objective

Separate the hosted **DAA-EL V1.0** environment from the newer **NexusFlow V4** environment.

Current problem:

```text
V1 Frontend ─┐
             ├── shared data/backend state
V4 Frontend ─┘
```

Target:

```text
V1 Frontend → V1 Backend → NEW V1 MongoDB

V4 Frontend → V4 Backend → V4 MongoDB
```

The V1 application behavior must remain unchanged. This work is environment/data isolation, not an application rewrite.

### Global rules

- Do NOT modify V4 MongoDB.
- Do NOT delete the old/shared database.
- Do NOT redesign V1.
- Do NOT change DAA algorithms.
- Do NOT expose MongoDB credentials.
- Store credentials only in environment variables.
- Keep V1 and V4 independently deployable.
- Test the real hosted flow.
- Never claim PASS without evidence.
- Execute prompts sequentially without approval checkpoints.
- Fix root causes and retest failures.

---

# PROMPT 101 — Audit V1/V4 Connections

### Description

Inspect the DAA-EL repository and determine exactly how V1 and V4 connect to MongoDB, the backend API, and Socket.IO.

### Tasks

1. Inspect backend startup/configuration, MongoDB connection code, frontend API configuration, Socket.IO configuration, `.env` examples, and deployment configuration.
2. Identify:
   - V1 frontend URL
   - V1 backend URL
   - V1 MongoDB variable
   - V4 frontend URL
   - V4 backend URL
   - V4 MongoDB variable.
3. Determine whether V1 and V4 currently share:
   - MongoDB cluster/database
   - backend service
   - API URL
   - Socket.IO endpoint.
4. Do not change production configuration yet.
5. Produce a short evidence-based audit.

### Pass condition

The exact current V1/V4 connection map and required configuration changes are known.

---

# PROMPT 102 — Configure New V1 MongoDB

### Description

Connect the existing V1 backend to the newly created MongoDB project/cluster.

### Tasks

1. Use the NEW MongoDB connection created specifically for V1.
2. Configure it only through the V1 backend environment variable.
3. Use an explicit V1 database name such as `nexusflow_v1`, unless the existing application requires another name.
4. Never hard-code credentials.
5. Update `.env.example` only with placeholders.
6. Do not modify V4 variables.
7. Do not delete the old database.
8. Verify V1 backend connects successfully.

### Pass condition

V1 backend starts and connects to the new V1 MongoDB.

---

# PROMPT 103 — Deploy and Verify V1

### Description

Deploy V1 using the new database and confirm normal functionality.

### Tasks

1. Update the V1 hosted backend MongoDB environment variable.
2. Redeploy/restart V1.
3. Verify:
   - backend starts
   - MongoDB connects
   - API responds
   - authentication works
   - frontend connects
   - Socket.IO connects.
4. Create a uniquely named V1 test team/project/task.
5. Confirm the data is stored in the new V1 MongoDB.
6. Confirm normal V1 DAA/task behavior remains unchanged.
7. Do not modify V4.

### Pass condition

V1 works normally against the new MongoDB.

---

# PROMPT 104 — Prove V1/V4 Isolation

### Description

Perform real cross-environment isolation tests.

### Tasks

### Test A — V1 → V4

1. Create a uniquely named V1 test object.
2. Confirm it exists in V1.
3. Open V4.
4. Confirm the V1 object does NOT appear in V4.

### Test B — V4 → V1

1. Create a uniquely named V4 test object.
2. Confirm it exists in V4.
3. Open V1.
4. Confirm the V4 object does NOT appear in V1.

### Test C — Socket.IO isolation

Verify:

```text
V1 creates task → V1 receives event → V4 does NOT
V4 creates task → V4 receives event → V1 does NOT
```

### Test D — Authentication/data isolation

Confirm V1 and V4 continue using their own backend/session/data flows.

### Pass condition

All four isolation tests pass.

---

# PROMPT 105 — Regression, Security and Final Report

### Description

Finish the V1 separation safely and document it.

### Tasks

1. Run V1 regression checks:
   - TypeScript/build where applicable
   - backend startup
   - MongoDB connectivity
   - login
   - team/project creation
   - task creation/update
   - DAA algorithm flows
   - Socket.IO/realtime.
2. Verify V4 still works and was not modified.
3. Search for accidental hard-coded MongoDB URIs or credentials.
4. Confirm secrets exist only in deployment environment variables.
5. Do NOT delete the old/shared MongoDB.
6. Create:

```text
V1_MONGODB_SEPARATION_REPORT.md
```

Include:
- before architecture
- after architecture
- V1 environment variables
- V1 MongoDB
- V1 backend/frontend
- V4 backend/frontend
- isolation tests
- Socket.IO isolation
- regression results
- security checks
- remaining migration considerations.

### Final architecture

```text
V1 DAA-EL
Frontend
   ↓
V1 Backend
   ↓
NEW V1 MongoDB


V4 NexusFlow
Frontend
   ↓
V4 Backend
   ↓
V4 MongoDB
```

### Pass condition

V1 and V4 are independently working and their data/realtime events cannot cross environments.

---

# MASTER EXECUTION PROMPT — RUN 101–105

Read this entire MD first.

Execute **101 → 102 → 103 → 104 → 105** in one continuous pass.

For every prompt:

```text
inspect
→ implement/configure
→ test
→ fix
→ retest
→ continue
```

Use the real DAA-EL repository and real hosted V1 environment.

The newly created MongoDB project is the V1 database target.

The existing V4 MongoDB must remain untouched.

Do not delete the old database.

Do not expose credentials.

Do not fabricate results.

If a test fails, diagnose and fix the root cause before continuing.

At the end, create `V1_MONGODB_SEPARATION_REPORT.md`.

## Definition of Done

PASS only when:

```text
V1 Frontend → V1 Backend → NEW V1 MongoDB
V4 Frontend → V4 Backend → V4 MongoDB
```

and:

- V1 data is invisible to V4.
- V4 data is invisible to V1.
- V1 Socket.IO events do not reach V4.
- V4 Socket.IO events do not reach V1.
- V1 functionality remains intact.
- V4 functionality remains intact.
- No credentials are committed.
- No database is deleted.
- Final report contains real evidence.

Final status must be:

`PASS`, `PARTIAL`, or `BLOCKED`.

Only report PASS when the real isolation tests succeed.
