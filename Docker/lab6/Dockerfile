FROM python:3.11-slim 

WORKDIR /lab6

RUN pip install flask

COPY ./Docker-3/app.py . 

ENV APP_MODE=production

ENV APP_REGION=canada-west

EXPOSE 8080

CMD [ "python", "app.py" ]
