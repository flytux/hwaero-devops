$ docker run -it --entrypoint /bin/bash -v "$PWD":/app -w /app haiqv-ai:base

$  pip download -r requirements.txt downloads

$ twine upload --repository-url http://192.168.45.245:9000/repository/pypi-local/ downloads/*

$ pip install -r requiremets.txt --index-url http://192.168.45.245:9000/repository/pypi-local/simple --trusted-host 192.168.45.245
