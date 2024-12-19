FROM python:3.12-slim AS builder
COPY --from=ghcr.io/astral-sh/uv:latest /uv /uvx /bin/
ENV UV_COMPILE_BYTECODE=1 UV_LINK_MODE=copy
WORKDIR /code

ARG ENV
ARG DEV_OPTS
RUN if [ "$ENV" = "test" ]; then \
    DEV_OPTS=--no-dev; \
    fi

RUN --mount=type=cache,target=/root/.cache/uv \
--mount=type=bind,source=uv.lock,target=uv.lock \
--mount=type=bind,source=pyproject.toml,target=pyproject.toml \
uv sync --frozen --no-install-project $DEV_OPTS

COPY uv.lock pyproject.toml /code/

RUN --mount=type=cache,target=/root/.cache/uv \
    uv sync --frozen $DEV_OPTS


FROM python:3.12-slim AS production
ENV UV_COMPILE_BYTECODE=1 UV_LINK_MODE=copy
# Copy the application from the builder
COPY --from=builder --chown=app:app /code /code
# Place executables in the environment at the front of the path
ENV PATH="/code/.venv/bin:$PATH"

WORKDIR /code

COPY ./snippets /code/snippets