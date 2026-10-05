---
name: python-testing-practices
description: Use when writing, reviewing, or refactoring Python tests, especially pytest tests involving fixtures, mocks, fakes, monkeypatching, external services, databases, or AI clients.
---

# Python Testing Practices

Use these as practical defaults, not laws. First follow the repository's existing test framework, naming, typing, and fixture conventions. Prefer tests that observe behavior through a public interface and remain useful after internal refactoring.

## Choose the test double by scope

Start with real collaborators when they are cheap, deterministic, and safe. Otherwise choose the smallest substitute that keeps the test clear:

| Need | Default |
| --- | --- |
| Change environment variables, cwd, a module global, or a mapping | pytest's `monkeypatch` |
| Replace one function result or a small attribute for one test | `mocker.patch` / `mocker.patch.object` |
| Verify a small, meaningful interaction at an external boundary | `mocker.Mock`, `AsyncMock`, or `create_autospec` |
| Represent a stateful collaborator used by several tests | A small hand-written fake |
| Exercise adapter wiring or persistence behavior | A real test dependency, test database, or integration test |

`monkeypatch` is for controlled ambient-state changes, not the default replacement for dependency injection. If a dependency is already injected, replace the injected value directly instead of patching its construction site.

Use a fake when mock setup is becoming a second language, when the collaborator has meaningful state, or when its behavior matters across several tests. Give the fake the public contract (a `Protocol` is useful); do not reproduce the real implementation. Keep it deterministic, minimal, and isolated per test.

## Mocking rules

- Use `pytest-mock`'s `mocker` fixture when the project has `pytest-mock`; otherwise use the project's existing mock API.
- Prefer `autospec=True` or `create_autospec` for mocks based on real callables, classes, or protocols. For an object dependency, `mocker.create_autospec(SomeProtocol, instance=True)` is preferable to `spec=[...]` when a stable type exists. Use `spec` when autospeccing cannot safely inspect the object or no stable type exists.
- Patch where the system under test looks up a name, not where that name was originally defined.
- Assert calls only when the interaction is part of the behavior: for example, an outbound request, persistence operation, or required callback. Otherwise assert the returned state or observable result.
- Keep patches narrow and test-scoped. Avoid broad module-wide or session-wide mocks.
- Do not turn one mock into a miniature fake by configuring many methods and pieces of state; switch to a hand-written fake when that happens.

## Pytest shape

- Prefer plain `test_...` functions and explicit fixture arguments.
- Keep fixtures close to their consumers; move them to `conftest.py` only when they are genuinely shared.
- Avoid `autouse` fixtures and long fixture chains that hide setup.
- Use parametrization for the same behavior across clearly named cases.
- Use built-ins such as `tmp_path`, `capsys`, and `caplog` instead of hand-rolled cleanup or global state.
- Name tests after observable behavior and the relevant condition.

## Review questions

Before keeping a test, ask:

1. What behavior would a real caller care about?
2. Is the test double smaller than the collaborator it replaces?
3. Would a harmless internal refactor break this test?
4. Is each expected value independent of the implementation?
5. Does the test isolate mutable state and clean up process-wide changes?

When a fake, mock, or fixture is complex, treat that complexity as feedback about the production seam. Improve the seam only when it makes the production design clearer; do not add abstractions solely to satisfy a test.

## Example

For a service receiving a repository and an AI model, use a fake repository when tests need realistic storage behavior, but patch a one-off model failure when that is all the test needs:

```python
def test_generate_saves_the_model_answer(service, repository, model):
    repository.records["r1"] = Record(text="source text")
    model.generate.return_value = "summary"

    assert service.generate("r1") == "summary"
    assert repository.summaries["r1"] == "summary"
    model.generate.assert_called_once_with("Prefix: source text")


def test_generate_propagates_model_failure(service, repository, model):
    repository.records["r1"] = Record(text="source text")
    model.generate.side_effect = RuntimeError("unavailable")

    with pytest.raises(RuntimeError, match="unavailable"):
        service.generate("r1")
```

Here `repository` is a fake because its state is part of the scenario; `model` is a mock because this test needs one return value or failure and one outbound-call assertion. Adapt the example to the project's actual types and fixtures.

## Common mistakes

- Mocking every collaborator: use real value objects and fakes where behavior matters.
- Using `monkeypatch` for injected dependencies: pass the substitute through the constructor or function argument.
- Creating an oversized fake: implement only the contract required by the tests.
- Asserting every call: keep interaction assertions for actual contracts.
- Using unbounded mocks: add a spec or autospec so typos and signature drift fail early.
