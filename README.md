# Session 2 baseline app

The starting point for every session 2 lesson, and the baseline for your own
repository if yours is incomplete.

It is the `hello` server from [session 1 lesson 02](../../lessons/s1-02-web-server.html),
the multi-stage distroless `Dockerfile` from [lesson 04](../../lessons/s1-04-advanced-dockerfile.html),
and the Deployment and Service from [lesson 08](../../lessons/s1-08-health-and-resources.html)
with its probes, requests and limits.

Three things are new, and each exists because a pipeline needs it:

- **`main_test.go`** — session 1 never wrote a test. A workflow that runs
  `go test` needs something to run, and a job that passes because there were no
  tests teaches the wrong lesson.
- **`newMux()` and `port()` in `main.go`** — session 1 registered the handlers on
  the default mux inside `main`. A test can only reach a handler it can build, so
  the routes moved into a function. Nothing about the server's behaviour changed.
- **`VERSION`** — one line of text, the version the publishing workflow in
  [lesson 06](../../lessons/s2-06-build-and-push.html) uses as the image tag.
  Bumping it in a pull request is how a release happens.

## If your own repo is bare

Session 2 builds a pipeline around your own `hello` repository. If you did not
finish session 1's exercise, or your repo holds only lesson 02's `main.go`, copy
this directory into it and work from here:

```
cp -r kubernetes-and-devops/examples/s2-demo-app/. hello/
```

See [lesson 00](../../lessons/s2-00-preflight.html) for the rest of the setup.

## Run it

```
go test ./...
docker build -t hello:local .
docker run --rm -p 8080:8080 hello:local
curl localhost:8080/ localhost:8080/healthz
```

## About the image name in `k8s/`

The manifests point at `ghcr.io/balazsrakhely/cicd-demo:0.1.0` — the instructor's
demo repository. Change it to your own package before you apply them:

```
sed -i "s|image: .*|image: ghcr.io/<you>/hello:0.1.0|" k8s/deployment.yaml
```

Two of the session's workflows rewrite that line themselves rather than trusting
it: the e2e job in [lesson 04](../../lessons/s2-04-jobs-and-artifacts.html) points
it at the image the run just built. Treating the manifest's image as an input, not
a fact, is the same idea one layer up.
