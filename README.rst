=========================
django devops try project
=========================

Sandbox project to try devops staff, CI\CD etc.

Steps of work
=============

CircleCI
--------


1. Prepare project with at least one application to run simple test with ``pytest`` for first circleci workflow.

    1.1 Register at https://app.circleci.com/

    1.2 Setup your project

    1.3 Add ``.circleci`` folder with ``config.yml`` file

2. ``config.yml`` - `First working version with comments <https://github.com/LowerDeez/devops-try/blob/7bd2928acd0e23438e7816e846690f00f444e381/.circleci/config.yml>`_
3. ``config.yml`` - `Add some code style checks (black, pylama, isort, migrations linter) <https://github.com/LowerDeez/devops-try/commit/8ed4e02a81c5302bcffb726b7baf0d8bd1d2d5eb>`_
4. `Add invoke tasks to update code style <https://github.com/LowerDeez/devops-try/commit/0c20d8161beb7731d12d2a32217fd5bb2b23d724>`_.
5. `Fix isort <https://github.com/LowerDeez/devops-try/commit/19b440d16fea8bb191c77c5ff89a6092fb2c0cbd>`_.
6. `Fix django-linter <https://github.com/LowerDeez/devops-try/commit/ecd8b3086ee06f150a29558f49c550d634b77ebf>`_.
7. Configure docker for "production"
    7.1 Related articles
        https://cookiecutter-django.readthedocs.io/en/latest/deployment-with-docker.html
        https://testdriven.io/blog/dockerizing-django-with-postgres-gunicorn-and-nginx/
        https://testdriven.io/blog/django-lets-encrypt/
    7.2 Commands:
        docker-compose -f production.yml run --user root --rm django bash -c "cd server && python manage.py collectstatic --no-input --clear" - collect static
        docker-compose -f production.yml up -d --build
        docker-compose -f production.yml exec django bash -c "cd server && python manage.py migrate --noinput"
        docker-compose -f production.yml exec django bash -c "cd server && python manage.py createsuperuser"
        docker-compose -f production.yml stop
    7.3 `Commit <https://github.com/LowerDeez/devops-try/commit/f884276f5ad09816de9a2e64020ef0a3c8ece517>`_.