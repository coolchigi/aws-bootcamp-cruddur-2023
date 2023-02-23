FROM python:3.10-slim-buster

# Inside Container - make a new folder inside container
WORKDIR /backend-flask


# Outside Container -> Inside Container
COPY requirements.txt requirements.txt

# Install the python libraries used for the app
RUN pip3 install -r requirements.txt

#Outside Container -> Inside Container
# . means everything in current dir
# first . means -> /backend-flask(outside container)
# second . means /backend-flask(inside container)
COPY . .

# Set Env Variables(Env Vars)
ENV FLASK_ENV=development

EXPOSE ${PORT}

# CMD -> Command
# python3 -m flask run --host=0.0.0.0 --port=4567
CMD [ "python3", "-m" , "flask", "run", "--host=0.0.0.0", "--port=4567"]