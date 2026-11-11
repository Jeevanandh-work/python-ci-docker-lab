# Exp 3 – CI Pipeline: Python App, GitHub Actions and Docker Hub

## 1. Aim

To create a simple Python application with pytest tests, containerize it using Docker, and set up a GitHub Actions CI pipeline that tests the code and pushes the Docker image to Docker Hub automatically.

## 2. Procedure

1. Create a project folder `python-ci-docker-lab` and open it in VS Code.
2. Create `app.py` with an `add(a, b)` function and a main block that prints a message.
3. Create `tests/test_app.py` with a `test_add()` function that checks `add(2, 3) == 5` and `add(-1, 1) == 0`.
4. Create `requirements.txt` containing `pytest==8.3.2`.
5. Create `pytest.ini` with `pythonpath = .` so the test can import `app`.
6. Create a `Dockerfile` using `python:3.11-slim`, copy the files, install requirements and set `CMD ["python", "app.py"]`.
7. Create `.gitignore` with `__pycache__/`, `.venv/`, `.pytest_cache/`, `*.pyc`.
8. Create `.github/workflows/ci-dockerhub.yml` with steps: Checkout, Set up Python 3.11, Install deps, Run tests, Docker meta, QEMU, Buildx, Login to Docker Hub, Build and push.
9. Open a terminal and run `python app.py` to see the output.
10. Run `pytest -q` to run the tests.
11. Run `docker build -t <username>/python-ci-lab:local .` and `docker run --rm <username>/python-ci-lab:local`.
12. Create a new repository on GitHub (do not add README or .gitignore).
13. In GitHub, go to Settings → Secrets and variables → Actions and add `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` (Docker Hub access token).
14. In the terminal run `git init`, `git add .`, `git commit -m "init lab"`, `git branch -M main`, `git remote add origin <url>`, `git push -u origin main`.
15. Open GitHub → Actions tab and check the workflow run status.
16. Open `https://hub.docker.com/r/<username>/python-ci-lab` and check the tags `latest` and `sha-xxxxxxx`.
17. Run `git tag v1.0.0` and `git push origin v1.0.0` to publish a version-tagged image.
18. Check Docker Hub again for the `v1.0.0` tag.

## 3. Output

Local run:

```
Hello from Python CI Lab!
2 + 3 = 5
```

pytest:

```
1 passed in 0.02s
```

Docker run:

```
Hello from Python CI Lab!
2 + 3 = 5
```

Git push:

```
 * [new branch]      main -> main
branch 'main' set up to track 'origin/main'.
```

GitHub Actions: workflow `ci-dockerhub` shows a green tick (Success) with all steps passed.

Docker Hub: repository `<username>/python-ci-lab` shows tags `latest`, `sha-xxxxxxx` and `v1.0.0`.

## 4. Result

Thus, a Python application with unit tests was containerized, pushed to GitHub, and the GitHub Actions workflow successfully ran pytest, built the Docker image and pushed it to Docker Hub with the tags `latest`, `sha-xxxxxxx` and `v1.0.0`.

---

## Execution Guide (VS Code)

**Step 1 – Create the project folder**

```powershell
cd C:\Users\jeeva\Downloads
mkdir python-ci-docker-lab
cd python-ci-docker-lab
code .
```

**Step 2 – Create the files** (already created in this workspace; copy them into the new folder if needed)

```
app.py
tests/test_app.py
requirements.txt
pytest.ini
Dockerfile
.gitignore
.github/workflows/ci-dockerhub.yml
```

**Step 3 – Create a virtual environment and install pytest**

```powershell
python -m venv .venv
.\.venv\Scripts\Activate.ps1
pip install -r requirements.txt
```

**Step 4 – Run the app and tests**

```powershell
python app.py
pytest -q
```

Expected: `Hello from Python CI Lab!` / `2 + 3 = 5` and `1 passed`.

**Step 5 – Build and run the Docker image** (replace `<username>` with your Docker Hub username)

```powershell
docker build -t <username>/python-ci-lab:local .
docker run --rm <username>/python-ci-lab:local
```

**Step 6 – Create the GitHub repository**

Open `https://github.com/new`, name it `python-ci-docker-lab`, keep it Public, do **not** add README/.gitignore, click **Create repository**. Copy the repository URL.

**Step 7 – Add Docker Hub secrets**

Open `https://hub.docker.com/settings/security` → **New Access Token** → copy the token.
Then open your repo → **Settings** → **Secrets and variables** → **Actions** → **New repository secret**:

- `DOCKERHUB_USERNAME` = your Docker Hub username
- `DOCKERHUB_TOKEN` = the access token you copied

**Step 8 – Push the code** (replace `<url>` with your repository URL)

```powershell
git init
git add .
git commit -m "init lab"
git branch -M main
git remote add origin <url>
git push -u origin main
```

**Step 9 – Watch the workflow**

Open `https://github.com/Jeevanandh-work/python-ci-docker-lab/actions` → click the `ci-dockerhub` run → all steps should be green.

If the run failed with `Username and password required`, the secrets were missing. Add them (Step 7), then open the failed run → **Re-run all jobs**.

**Step 10 – Verify on Docker Hub**

Open `https://hub.docker.com/r/<username>/python-ci-lab/tags` → tags `latest` and `sha-xxxxxxx` should be listed.

If the repository does not exist yet, create it once at `https://hub.docker.com/repository/create` with the name `python-ci-lab` and visibility Public.

**Step 11 – Release with a tag**

```powershell
git tag v1.0.0
git push origin v1.0.0
```

Open the Actions tab again (new run) and then Docker Hub to see the `v1.0.0` tag.

---

## Troubleshooting

1. **`ModuleNotFoundError: No module named 'app'`** – `pytest.ini` is missing or not in the project root. Create it with `pythonpath = .` and run `pytest -q` again.
2. **`Activate.ps1 cannot be loaded because running scripts is disabled`** – run `Set-ExecutionPolicy -Scope Process -ExecutionPolicy Bypass` and then activate again.
3. **Workflow fails with `Username and password required`** – the secrets `DOCKERHUB_USERNAME` and `DOCKERHUB_TOKEN` are not set (or the names are misspelled). Add them under Settings → Secrets and variables → Actions, then use **Re-run all jobs**. The token must be a Docker Hub access token, not your password.
4. **`docker: error during connect`** – Docker Desktop is not running. Start Docker Desktop and wait until it says "Engine running", then run the build again.
