# Recon-ng untuk lab OSINT
# Kode recon-ng disalin ke dalam image (Dockerfile resmi tidak melakukannya),
# dan modul marketplace sudah terpasang supaya peserta tidak perlu mengunduh lagi.
FROM python:3.11-slim

ARG RECON_NG_VERSION=5.1.2

ENV PYTHONDONTWRITEBYTECODE=1 \
    PYTHONUNBUFFERED=1

WORKDIR /recon-ng

ADD https://github.com/lanmaster53/recon-ng/archive/refs/tags/v${RECON_NG_VERSION}.tar.gz /tmp/recon-ng.tar.gz

RUN tar xzf /tmp/recon-ng.tar.gz --strip-components=1 \
 && rm /tmp/recon-ng.tar.gz \
 && pip install --no-cache-dir -r REQUIREMENTS

# Dependensi tambahan yang dibutuhkan modul marketplace.
# censys dikunci <2.2 karena modul censys_subdomains masih memakai API lama (CensysCertificates).
RUN pip install --no-cache-dir pyaes PyPDF3 olefile beautifulsoup4 shodan cryptography "censys<2.2"

# Pasang semua modul marketplace ke ~/.recon-ng (ikut tersalin ke volume saat pertama dijalankan)
COPY install-modules.rc /tmp/install-modules.rc
RUN ./recon-ng --no-analytics -r /tmp/install-modules.rc \
 && rm /tmp/install-modules.rc

VOLUME ["/root/.recon-ng"]

ENTRYPOINT ["./recon-ng", "--no-analytics"]
