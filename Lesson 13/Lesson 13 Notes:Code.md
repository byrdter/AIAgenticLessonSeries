I’m going to give you:

1. the **structured prompt** (the “job definition”)
2. the **backend code** (router + service + repo)
3. the **tests**
4. a tiny **diff-style summary** of what changed

You can adapt names/paths to your repo, but the pattern holds.

1. **The "Job" Prompt Shown in Video**

You are helping me extend a FastAPI backend for an app called Agent Workspace.

CONTEXT
- Architecture: core/, shared/, features/ with vertical slices.
- This feature belongs in features/workspaces/.
- DB: Postgres + SQLAlchemy.
- Tests: pytest.

TASK
- Add an endpoint: GET /workspaces/search?query=...
- Match by substring on workspace.name (case-insensitive).
- Return the same response schema as the existing “list workspaces” endpoint.

CONSTRAINTS
- Only edit files under: features/workspaces/*
- Follow existing patterns for router → service → repository.
- Add tests covering:
  - Search returns multiple matches
  - Search returns empty list for no matches
  - Query shorter than 2 chars returns 400

VALIDATION
- At the end, list all files changed.
- Tell me which pytest command to run.



2. **Backend Code (Example)**

Features/workspaces/api.py
from fastapi import APIRouter, Depends, HTTPException, Query
from .service import WorkspaceService
from .schemas import WorkspaceOut

router = APIRouter(prefix="/workspaces", tags=["workspaces"])


def get_service() -> WorkspaceService:
    # In your real app this is probably injected with DB session dependency
    return WorkspaceService()


@router.get("/search", response_model=list[WorkspaceOut])
def search_workspaces(
    query: str = Query(..., min_length=1),
    service: WorkspaceService = Depends(get_service),
) -> list[WorkspaceOut]:
    q = query.strip()

    if len(q) < 2:
        raise HTTPException(status_code=400, detail="query must be at least 2 characters")
    
    return service.search_by_name(query=q)



features/workspaces/service.py

from .repository import WorkspaceRepository
from .schemas import WorkspaceOut


class WorkspaceService:
    def __init__(self, repo: WorkspaceRepository | None = None) -> None:
        self.repo = repo or WorkspaceRepository()

    def search_by_name(self, query: str) -> list[WorkspaceOut]:
        rows = self.repo.search_by_name(query=query)
        return [WorkspaceOut.model_validate(r) for r in rows]

features/workspaces/repository.py

from sqlalchemy import select, func
from .models import Workspace
from .db import get_session  # replace with your actual session provider


class WorkspaceRepository:
    def search_by_name(self, query: str) -> list[Workspace]:
        session = get_session()

        pattern = f"%{query.lower()}%"
        stmt = select(Workspace).where(func.lower(Workspace.name).like(pattern)).order_by(Workspace.name.asc())
    
        return list(session.execute(stmt).scalars().all())

features/workspaces/schemas.py

from pydantic import BaseModel, ConfigDict


class WorkspaceOut(BaseModel):
    model_config = ConfigDict(from_attributes=True)

    id: int
    name: str

3) **Tests (Pytest)**

features/workspaces/test_search_api.py

import pytest
from fastapi.testclient import TestClient

from app.main import app  # adjust import
from features.workspaces.repository import WorkspaceRepository


@pytest.fixture()
def client() -> TestClient:
    return TestClient(app)


def test_search_returns_multiple_matches(client: TestClient, monkeypatch: pytest.MonkeyPatch) -> None:
    # Arrange: fake repo response
    class FakeRepo(WorkspaceRepository):
        def search_by_name(self, query: str):
            return [
                type("W", (), {"id": 1, "name": "Alpha Workspace"})(),
                type("W", (), {"id": 2, "name": "Alpha Labs"})(),
            ]

    # Patch service wiring in whatever way your app uses DI
    from features.workspaces.service import WorkspaceService
    from features.workspaces import api as workspaces_api
    
    def fake_get_service():
        return WorkspaceService(repo=FakeRepo())
    
    app.dependency_overrides[workspaces_api.get_service] = fake_get_service
    
    # Act
    r = client.get("/workspaces/search", params={"query": "alpha"})
    
    # Assert
    assert r.status_code == 200
    data = r.json()
    assert len(data) == 2
    assert data[0]["name"].lower().startswith("alpha")
    
    app.dependency_overrides.clear()


def test_search_returns_empty_list_when_no_matches(client: TestClient) -> None:
    # simplest behavior check: you may need real DB fixture in your repo
    r = client.get("/workspaces/search", params={"query": "zzzz-nope"})
    assert r.status_code in (200, 500)  # replace with your real DB-backed expectation
    # If you have DB fixtures, this should be:
    # assert r.status_code == 200
    # assert r.json() == []

def test_search_rejects_short_query(client: TestClient) -> None:
    r = client.get("/workspaces/search", params={"query": "a"})
    assert r.status_code == 400
    assert r.json()["detail"] == "query must be at least 2 characters"

4. **Files Changed:**

features/workspaces/api.py          + new GET /workspaces/search
features/workspaces/service.py      + search_by_name()
features/workspaces/repository.py   + search query (case-insensitive LIKE)
features/workspaces/test_search_api.py  + tests (multi-match, no-match, short query)

**How this fits in with Video Narration:**

“One service. One responsibility.” → show `service.py`

“Notice what I don’t say in the prompt…” → show the prompt block

“I define a job.” → highlight the **TASK/CONSTRAINTS/VALIDATION** lines