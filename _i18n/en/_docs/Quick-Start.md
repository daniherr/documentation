## PolySwarm Micro-Engine Quick Start Guide

We provide you with a skeleton microengine written in `Python3`.
* [**polyswarm/microengine**](https://github.com/polyswarm/microengine) 
There are some backends available by default with this, including: `ClamAV, YARA, EICAR-only, Multi(Clam+YARA), and Scratch`.
If you're looking to get started right away, please familiarize yourself with `src/microengine/clamav.py` and `src/microengine/yarae.py`. 
The ClamAV microengine we provide feeds a byte stream to `clamd` (the clam daemon), while the `YARA` engine requires a file written to disk.

You will need to write your own `scan()` method for any microengine you create. 
The microengine skeleton expects 3 Returns from `scan()`:
```
            (bool, bool, str): Tuple of bit, verdict, metadata

            bit (bool): Whether to include this artifact in the assertion or not
            verdict (bool): Whether this artifact is malicious or not
            metadata (str): Optional metadata about this artifact
```

Let's get into it.

```bash
vim src/microengine/YourMicroengine.py 
``` 
```python
from microengine import Microengine
#import yourmodule_if_necessary
class YourMicroengine(Microengine):
	async def scan(self, guid, content):
		bit = False
		verdict = False
		metadata = ''

		#do your scanning, the bounty'd file is in 'content' as a byte string,
		#so format it as required by your analysis backend
		#set bit=true if you want to assert on this artifact, verdict=true if it's mal,
		#metadata='whatever extra info you have'
		return bit, verdict, metadata
```

Once you've implemented your scanning logic, you can easily test(locally) by:
```bash
pip(3) install -r requirements.txt
microengine-unit-test --malware_repo dummy --backend eicar
```
or within a docker container:
```bash
docker build -t polyswarm/microengine -f docker/Dockerfile .
docker run -it polyswarm/microengine bash
bash-4.4# microengine-unit-test --malware_repo dummy --backend eicar
```
expected output:
```sh
root@56c56f39c1fd:/usr/src/app# microengine-unit-test --malware_repo dummy --backend eicar
Using account: 0x05328f171b8c1463eaFDACCA478D9EE6a1d923F8
.
----------------------------------------------------------------------
Ran 1 test in 0.787s

OK

```