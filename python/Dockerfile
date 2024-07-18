FROM haiqv-ai:base

WORKDIR /app

COPY requirements.txt .
RUN pip install -r requirements.txt --index-url http://nexus.ai-dev/repository/pypi-repos/simple/ --trusted-host nexus.ai-dev

COPY ./router /app/router
COPY ./src /app/src
COPY ./main.py /app/main.py

ENV STAGE='dev'

CMD ["uvicorn", "main:app", "--host", "0.0.0.0", "--port", "80", "--loop", "uvloop"]
