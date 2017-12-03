FROM python:3.6

WORKDIR /app

ENV PYTHONUNBUFFERED 1

ADD ./requirements.txt .
RUN pip install -U pip
RUN pip install -Ur requirements.txt

COPY . .

EXPOSE 8000

CMD ["python3", "app.py"]
