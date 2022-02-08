ARG PYTHON_VERSION=3.9-slim-bullseye

# define an alias for the specfic python version used in this file.
FROM python:${PYTHON_VERSION} as python

# Python build stage
FROM python as python-build-stage

ARG BUILD_ENVIRONMENT=production

# Install apt packages
RUN apt-get update && apt-get install --no-install-recommends -y \
  # dependencies for building Python packages
  build-essential \
  # psycopg2 dependencies
  libpq-dev


# Python 'run' stage
FROM python as python-run-stage

ARG BUILD_ENVIRONMENT=production
ARG APP_HOME=/code

ENV PYTHONUNBUFFERED 1
ENV PYTHONDONTWRITEBYTECODE 1
ENV BUILD_ENV ${BUILD_ENVIRONMENT}

WORKDIR ${APP_HOME}

# create the app user
RUN addgroup --system django && adduser --system --ingroup django django


# Install required system dependencies
RUN apt-get update && apt-get install --no-install-recommends -y \
  # psycopg2 dependencies
  libpq-dev \
  # Translations dependencies
  gettext \
  # git
  git \
  # cleaning up unused files
  && apt-get purge -y --auto-remove -o APT::AutoRemove::RecommendsImportant=false \
  && rm -rf /var/lib/apt/lists/*

# Install pipenv
RUN pip install --upgrade pip && pip install pipenv

# Copy pipfile and install it as a system packages
COPY ./server/Pipfile ./server/Pipfile.lock ${APP_HOME}/

# No need in virtualenv as we are already inside container
RUN pipenv install --dev --system


COPY --chown=django:django ./compose/production/django/entrypoint /entrypoint
RUN sed -i 's/\r$//g' /entrypoint
RUN chmod +x /entrypoint


COPY --chown=django:django ./compose/production/django/start /start
RUN sed -i 's/\r$//g' /start
RUN chmod +x /start
COPY --chown=django:django ./compose/production/django/celery/worker/start /start-celeryworker
RUN sed -i 's/\r$//g' /start-celeryworker
RUN chmod +x /start-celeryworker


COPY --chown=django:django ./compose/production/django/celery/beat/start /start-celerybeat
RUN sed -i 's/\r$//g' /start-celerybeat
RUN chmod +x /start-celerybeat


COPY ./compose/production/django/celery/flower/start /start-flower
RUN sed -i 's/\r$//g' /start-flower
RUN chmod +x /start-flower


# copy application code to WORKDIR
COPY --chown=django:django . ${APP_HOME}

#RUN mkdir ${APP_HOME}/server/app/static
#RUN mkdir ${APP_HOME}/server/app/uploads

# make django owner of the WORKDIR directory as well.
RUN chown -R django:django ${APP_HOME}

#RUN chown django:django ${APP_HOME}/server/app/static
#RUN chown django:django ${APP_HOME}/server/app/uploads

#RUN cd server && python manage.py collectstatic --noinput

RUN mkdir -p ${APP_HOME}/server/app/static && \
    mkdir -p ${APP_HOME}/server/app/uploads && \
    chown -R django:django ${APP_HOME} # && \
#    cd server && python manage.py collectstatic --noinput

USER django

ENTRYPOINT ["/entrypoint"]
