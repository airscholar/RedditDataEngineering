version: '3'

x-airflow-common:
  &airflow-common
  build:
    context: .
    dockerfile: Dockerfile
  image: custom-airflow:2.7.1-python3.9
  env_file:
    - airflow.env
  volumes:
    - ./config:/opt/airflow/config
    - ./dags:/opt/airflow/dags
    - ./data:/opt/airflow/data
    - ./etls:/opt/airflow/etls
    - ./logs:/opt/airflow/logs
    - ./pipelines:/opt/airflow/pipelines
    - ./plugins:/opt/airflow/plugins
    - ./tests:/opt/airflow/tests
    - ./utils:/opt/airflow/utils
   # - ./airflow.cfg:/opt/airflow/airflow.cfg
    - ./requirements.txt:/opt/airflow/requirements.txt
  depends_on:
    - postgres
    - redis

services:
  postgres:
    image: postgres:12
    environment:
      POSTGRES_USER: postgres
      POSTGRES_PASSWORD: postgres
      POSTGRES_DB: airflow_reddit
      POSTGRES_PORT: 5432
    ports:
      - "5432:5432"

  redis:
    image: redis:latest
    ports:
      - "6379:6379"

  airflow-init:
    <<: *airflow-common
    command: >
      bash -c "pip install -r /opt/airflow/requirements.txt && airflow db init && airflow db upgrade && airflow users create --username admin --firstname admin --lastname admin --role Admin --email airflow@airflow.com --password admin"
    restart: "no"

  airflow-webserver:
    <<: *airflow-common
    command: webserver
    ports:
      - "8080:8080"

  airflow-scheduler:
    <<: *airflow-common
    command: scheduler

  airflow-worker:
    <<: *airflow-common
    command: celery worker
