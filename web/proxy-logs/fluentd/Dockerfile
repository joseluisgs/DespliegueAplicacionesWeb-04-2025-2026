FROM fluent/fluentd:latest

# Cambiar a root para instalar gem
USER root

# Instalar plugin Elasticsearch
RUN gem install fluent-plugin-elasticsearch --no-document

# Volver a usuario fluent
USER fluent
