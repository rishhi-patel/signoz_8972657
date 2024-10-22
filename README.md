# INFO8985_monolith
otel for a python monolithic app

```bash
pip install -r requirements.txt
ansible-playbook playbook.yml
```

This is based on [the open telemetry docs](https://opentelemetry.io/docs/languages/python/getting-started/)

Start at, "Create and launch an HTTP Server." When you test the app, add `rolldice` to the end of the url.

When it becomes time to,"Send telemetry to an OpenTelemetry Collector," run `docker-compose up`. In another terminal window run:

```bash
export OTEL_PYTHON_LOGGING_AUTO_INSTRUMENTATION_ENABLED=true
opentelemetry-instrument --logs_exporter otlp flask run -p 8080
```

You should see the open telemetry output in the terminal that `docker-compose up` was run in. Upload a cut and paste of the output in that window. We will be re-using the app.py from this in next week's lab.

