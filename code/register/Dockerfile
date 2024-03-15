FROM python:3.13.0a4-slim

# pip install
COPY requirements.txt /scripts/requirements.txt
RUN pip install -r /scripts/requirements.txt

# only for local testing
COPY register.py /scripts/register.py

# will be overwritten by kf pipeline
ENTRYPOINT [ "python", "/scripts/register.py" ]
