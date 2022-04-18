---

title: "python"
keywords: homepage
sidebar: mydoc_sidebar
permalink: python.html

---

# Python

### [WSGI vs ASGI](https://kangbk0120.github.io/articles/2022-02/cgi-wcgi-asgi)
- CGI(Common Gateway Interface) : When Client requested for data, server has to send the data back to Web Application. CGI is a standard/regulation for the language differences. CGI has to restart the application process everytime there's a new request, and this tend to be a huge burden for script language since it takes more time to execute. **This is the reason for WSGI came out (only for python)**
- WSGI(Web Server Gateway Interface): Django and Flask are not web servers, and web servers don't support Python. Therefore, there has to be a "interpreter" between those two, and that is WSGI. But it is syncronized, which made it quite slow and hard to comprehensive many data. gunicorn is often used.
- ASGI(Asynchronous Server Gateway Interface) : FastAPI with Uvicorn succeed to make the gateway interface process asynchronous, solving problems of WSGI.
> Django, Flask, FastAPI are web framework, and gunicorn, uvicorn are web server.

### Django vs Flask vs FastAPI
- Django has a lot of third party apps but it's way heavier.
- Flask is for simple applications but it could be more difficult when you add more third party apps.
- FastAPI is very fast and easy to code. However the quantity of third party apps is quite low.
