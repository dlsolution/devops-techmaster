FROM python:3.9-slim-buster
WORKDIR /app
COPY . .
RUN pip install -r requirements.txt
ENV FLASK_APP=hello_flask.py
EXPOSE 5000
CMD ["flask", "run", "--host", "0.0.0.0"]