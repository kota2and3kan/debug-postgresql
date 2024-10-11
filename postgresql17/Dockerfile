FROM ubuntu:22.04 AS builder-debug-postgresql
RUN apt update \
 && apt install -y make gcc libreadline-dev zlib1g-dev curl libssl-dev libicu-dev pkgconf bison flex gdb vim-tiny libreadline8 openssl libc6 \
 && useradd -m -s /bin/bash postgres \
 && cd /usr/local/src \
 && curl -O https://ftp.postgresql.org/pub/source/v17.0/postgresql-17.0.tar.gz \
 && tar xzf postgresql-17.0.tar.gz \
 && chown -R postgres. postgresql-17.0/ \
 && su - postgres -c "cd /usr/local/src/postgresql-17.0/ && ./configure --enable-debug --with-openssl CFLAGS='-O0 -g3' && make world-bin -j 4 -s" \
 && cd /usr/local/src/postgresql-17.0/ \
 && make install-world-bin \
 && apt clean -y \
 && rm -rf /var/lib/apt/lists/* \
 && mkdir -p /usr/local/pgsql/data \
 && chown postgres. /usr/local/pgsql/data/ \
 && chmod 700 /usr/local/pgsql/data/ \
 && echo "export PGDATA=/usr/local/pgsql/data" >> /home/postgres/.profile \
 && echo "export PATH=/usr/local/pgsql/bin:$PATH" >> /home/postgres/.profile \
 && su - postgres -c "initdb --encoding=utf8 --no-local" \
 && echo "" >> /usr/local/pgsql/data/postgresql.conf \
 && echo "# Add user configuration" >> /usr/local/pgsql/data/postgresql.conf \
 && echo "listen_addresses = '*'" >> /usr/local/pgsql/data/postgresql.conf \
 && echo "logging_collector = on" >> /usr/local/pgsql/data/postgresql.conf \
 && echo "log_filename = 'postgresql-%Y-%m-%d.log'" >> /usr/local/pgsql/data/postgresql.conf \
 && echo "log_error_verbosity = verbose" >> /usr/local/pgsql/data/postgresql.conf \
 && echo "ssl = on" >> /usr/local/pgsql/data/postgresql.conf \
 && su - postgres -c 'yes "" | openssl req -new -x509 -days 365 -nodes -text -out /usr/local/pgsql/data/server.crt -keyout /usr/local/pgsql/data/server.key -subj "/CN=postgresql.example.com"' \
 && chmod 600 /usr/local/pgsql/data/server.key \
 && sed -i s/"127.0.0.1\/32"/"0.0.0.0\/0"/g /usr/local/pgsql/data/pg_hba.conf \
 && sed -i s/"::1\/128"/"::\/0"/g /usr/local/pgsql/data/pg_hba.conf
CMD ["/bin/bash"]
