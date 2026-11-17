# SpiderFoot untuk lab OSINT
# Basis python:3.11-slim (Debian) supaya semua dependensi tersedia sebagai wheel
# untuk amd64 dan arm64 (Mac M-series), tanpa perlu compiler.
FROM python:3.11-slim

ARG SF_VERSION=4.0

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1 \
    SPIDERFOOT_DATA=/var/lib/spiderfoot \
    SPIDERFOOT_LOGS=/var/lib/spiderfoot/log \
    SPIDERFOOT_CACHE=/var/lib/spiderfoot/cache

WORKDIR /home/spiderfoot

ADD https://github.com/smicallef/spiderfoot/archive/refs/tags/v${SF_VERSION}.tar.gz /tmp/spiderfoot.tar.gz

# pyyaml<6 (pin bawaan v4.0) gagal di-build di Python modern;
# SpiderFoot hanya memakai yaml.safe_load, jadi aman dinaikkan ke pyyaml 6.
RUN tar xzf /tmp/spiderfoot.tar.gz --strip-components=1 \
 && rm /tmp/spiderfoot.tar.gz \
 && sed -i 's/^pyyaml.*/pyyaml>=6,<7/' requirements.txt \
 && pip install --no-cache-dir -r requirements.txt \
 && useradd --system --no-create-home --home-dir /home/spiderfoot spiderfoot \
 && mkdir -p "$SPIDERFOOT_DATA" "$SPIDERFOOT_LOGS" "$SPIDERFOOT_CACHE" \
 && chown -R spiderfoot:spiderfoot "$SPIDERFOOT_DATA" /home/spiderfoot

USER spiderfoot
VOLUME ["/var/lib/spiderfoot"]
EXPOSE 5001

ENTRYPOINT ["python", "sf.py"]
CMD ["-l", "0.0.0.0:5001"]
