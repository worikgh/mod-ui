mod-ui
======

This is the UI for the MOD software. It's a webserver that delivers an HTML5 interface and communicates with mod-host.
It also communicates with the MOD hardware, but does not depend on it to run.

Install
-------

There are instructions for installing in a 64-bit Debian based Linux environment.
It will work in x86, other Linux distributions and Mac, but you might need to adjust the instructions.

The following packages will be required::

`sudo apt-get install virtualenv python3-pip python3-dev git build-essential libasound2-dev libjack-jackd2-dev liblilv-dev libjpeg-dev zlib1g-dev`

NOTE: libjack-jackd2-dev can be replaced by libjack-dev if you are using JACK1; libjpeg-dev is needed for python-pillow, at least on my system.

Start by cloning the repository::

```sh
git clone git://github.com/moddevices/mod-ui
cd mod-ui

Create a python virtualenv::

python3 -m venv myenv
source myenv/bin/activate

Due to incompatibilities from Python 3.10, if Python > 3.10 is in use run:

```sh
if [ -e myenv/lib/python3.10/site-packages/tornado/httputil.py ]; then
	sed -i -e 's/collections.MutableMapping/collections.abc.MutableMapping/' myenv/lib/python3.10/site-packages/tornado/httputil.py
elif [ -e myenv/lib/python3.11/site-packages/tornado/httputil.py ]; then
	sed -i -e 's/collections.MutableMapping/collections.abc.MutableMapping/' myenv/lib/python3.11/site-packages/tornado/httputil.py
elif [ -e myenv/lib/python3.12/site-packages/tornado/httputil.py ]; then
	sed -i -e 's/collections.MutableMapping/collections.abc.MutableMapping/' myenv/lib/python3.12/site-packages/tornado/httputil.py
	sed -i -e 's/import ssl/import _NOT_ssl/' myenv/lib/python3.12/site-packages/tornado/netutil.py
fi
```

Install python requirements::

`pip3 install -r requirements.txt`

Compile libmod_utils::

`make -C utils`

Run
---

Before running the server, you need to activate your virtualenv
(if you have just done that during installation, you can skip this step, but you'll need to do this again when you open a new shell)::

`source myenv/bin/activate`

mod-ui depends on mod-host and the JACK server running in order to make sound. So after you have JACK setup and running, in another terminal do::

`mod-host -n -p 5555 -f 5556`

If you do not have mod-host, you can tell mod-ui to fake the connection to the audio backend.
You will not get any audio, but you will be able to load plugins, make connections, save pedalboards and all that. For this, run::

`export MOD_DEV_HOST=1`

And now you are ready to start the webserver::

```sh
export MOD_DEV_ENVIRONMENT=0
python3 ./server.py
```

Setting the environment variables is needed when developing on a PC.
Open your browser and point to http://localhost:8888/.
