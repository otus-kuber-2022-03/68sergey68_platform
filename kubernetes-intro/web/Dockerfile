FROM nginx:latest
RUN useradd -u 1001 otus
WORKDIR /app
RUN chown -R otus:otus /app
COPY nginx.conf /etc/nginx/nginx.conf
EXPOSE 8000
STOPSIGNAL SIGQUIT
