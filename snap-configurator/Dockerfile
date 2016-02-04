#  A nice, small starting place
FROM gliderlabs/alpine:3.3

# 
RUN apk add --update curl && rm -rf /var/cache/apk/*

# Add the configuration json
ADD files/* /opt/

# 
CMD ["/bin/sh", "-c", "while true; do sleep 60; echo 'ping ping ping';  done"]
