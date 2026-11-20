# apogee-testing

Testing [Apogee](https://github.com/venomous-maker) apps without a server:
requests go straight into the app's HTTP kernel, in the same process, and the
responses come back with assertions that chain.

```grav
import "std"
import "apogee-testing"
use apogee.*

test "posts can be created" {
    let client = new TestClient(app.http())            // any HttpKernel
    client.get("/").assertOk().assertSee("Welcome")
    client.actingAs(user).postJson("/api/posts", {"title": "Hi"})
        .assertCreated()
        .assertJsonPath("data.title", "Hi")
    client.postJson("/api/posts", {}).assertInvalid(["title"])
}
```

Cookies the app sets are sent back on the next requests, so a session lasts
from one request to the next. A failed assertion throws `AssertionFailed`, and
its message says what was expected and what came back (with the start of the
body), so the test fails with it.

## The client

| | |
|---|---|
| `new TestClient(kernel)`, `TestClient.forHandler(fn(Request) -> Response)` | |
| `get`, `delete`, `post(uri, form)`, `put`, `patch` | form bodies (`application/x-www-form-urlencoded`) |
| `getJson`, `postJson(uri, data)`, `putJson`, `patchJson`, `deleteJson` | JSON bodies; `Accept: application/json` |
| `call(method, uri, body, contentType)` | anything else |
| `withHeader`, `withHeaders`, `withToken(token)`, `withCookie`, `withoutCookies` | sent with every request |
| `actingAs(user, guard = "web")`, `asGuest()` | requests made as a signed-in user: apogee-auth's guards take a user already on the request as theirs |
| `followingRedirects()` | follow redirects (up to 10) and return the last response |

## Assertions

| | |
|---|---|
| status | `assertStatus(code)`, `assertOk`, `assertCreated`, `assertAccepted`, `assertNoContent`, `assertBadRequest`, `assertUnauthorized`, `assertForbidden`, `assertNotFound`, `assertMethodNotAllowed`, `assertConflict`, `assertUnprocessable`, `assertTooManyRequests`, `assertSuccessful`, `assertServerError`, `assertRedirect(to)` |
| body | `assertSee(text)`, `assertDontSee(text)`, `assertSeeInOrder(texts)` |
| headers, cookies | `assertHeader(name, value)`, `assertHeaderMissing`, `assertCookie(name, value)`, `assertCookieExpired` |
| JSON | `assertJson(subset)`, `assertJsonPath("data.0.title", value)`, `assertJsonMissingPath`, `assertJsonCount(n, path)`, `assertExactJson(value)`; numbers compare by value |
| validation | `assertInvalid(["title"])` (a 422 with `errors` for those fields), `assertValid(fields)` |
| looking | `status`, `body()`, `header(name)`, `json()`, `jsonAt(path)`, `dump()` |

## Fakes in the other packages

Each package that talks to the outside world has its own fake for tests:
`events.fake()` (apogee-events), `queue.fake()` (apogee-queue),
`mailer.fake()` and `notifications.fake()` (apogee-mail), and
`Storage.fake("s3")` (apogee-storage).

## Tests

```bash
grav test tests/testing.grav
```
