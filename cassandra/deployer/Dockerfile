FROM debian:stretch-slim AS build

RUN apt-get update \
    && apt-get install -y --no-install-recommends gettext

ADD cassandra /tmp/chart
RUN cd /tmp && tar -czvf /tmp/cassandra.tar.gz chart

ADD apptest/deployer/cassandra /tmp/test/chart
RUN cd /tmp/test \
    && tar -czvf /tmp/test/cassandra.tar.gz chart/

ADD schema.yaml /tmp/schema.yaml

ARG REGISTRY
ARG TAG

RUN cat /tmp/schema.yaml \
    | env -i "REGISTRY=$REGISTRY" "TAG=$TAG" envsubst \
    > /tmp/schema.yaml.new \
    && mv /tmp/schema.yaml.new /tmp/schema.yaml

ADD apptest/deployer/schema.yaml /tmp/apptest/schema.yaml
RUN cat /tmp/apptest/schema.yaml \
    | env -i "REGISTRY=$REGISTRY" "TAG=$TAG" envsubst \
    > /tmp/apptest/schema.yaml.new \
    && mv /tmp/apptest/schema.yaml.new /tmp/apptest/schema.yaml

COPY --from=build /tmp/cassandra.tar.gz /data/chart/
COPY --from=build /tmp/test/cassandra.tar.gz /data-test/chart/
COPY --from=build /tmp/apptest/schema.yaml /data-test/
COPY --from=build /tmp/schema.yaml /data/

ENV WAIT_FOR_READY_TIMEOUT 1800
ENV TESTER_TIMEOUT 1800
