FROM alpine:3.10
# Install Python 3.7.5
RUN apk add --no-cache \
  python3=3.7.5-r1 \
  python3-dev=3.7.5-r1 \

# Add build dependencies
&& apk add --no-cache --virtual .build-deps  \
		gcc \
		libc-dev \
		libffi-dev \
		make

# Install pip
# if this is called "PIP_VERSION", pip explodes with "ValueError: invalid truth value '<VERSION>'"
ENV PYTHON_PIP_VERSION 19.3.1
# https://github.com/pypa/get-pip
ENV PYTHON_GET_PIP_URL https://github.com/pypa/get-pip/raw/ffe826207a010164265d9cc807978e3604d18ca0/get-pip.py
ENV PYTHON_GET_PIP_SHA256 b86f36cc4345ae87bfd4f10ef6b2dbfa7a872fbff70608a1e43944d283fd0eee
RUN set -ex; \
	\
	wget -O get-pip.py "$PYTHON_GET_PIP_URL"; \
	echo "$PYTHON_GET_PIP_SHA256 *get-pip.py" | sha256sum -c -; \
	\
	python3 get-pip.py \
		--disable-pip-version-check \
		--no-cache-dir \
		"pip==$PYTHON_PIP_VERSION" \
	; \
	pip --version; \
	\
	find /usr/local -depth \
		\( \
			\( -type d -a \( -name test -o -name tests \) \) \
			-o \
			\( -type f -a \( -name '*.pyc' -o -name '*.pyo' \) \) \
		\) -exec rm -rf '{}' +; \
	rm -f get-pip.py

WORKDIR /app
COPY . /app

# Install python libraries from requirements
RUN python3 -m pip install --no-cache -r requirements.txt \

# Remove build dependencies
&& apk del .build-deps

CMD ["gunicorn", "-b", "0.0.0.0:8000", "wsgi:application"]

# run with docker run -v $(pwd):/app -p 8000:8000 garretw/docker_flask_dash_app:0.1
