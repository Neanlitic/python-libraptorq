python-libraptorq
=================

Python 2.X CFFI_ bindings for libRaptorQ_ - C++11 implementation of RaptorQ
Forward Error Correction codes, as described in RFC6330_.

**Warning**: as far as I know (not a lawyer), there are lots of patents around
the use of this technology, which might be important for any high-profile and
commercial projects, especially in US and Canada.

Quoting `wikipedia on Raptor code`_:

  Raptor codes, as with fountain codes in general, encode a given message
  consisting of a number of symbols, k, into a potentially limitless sequence of
  encoding symbols such that knowledge of any k or more encoding symbols allows
  the message to be recovered with some non-zero probability.

  Raptor ("RApid TORnado") codes are the first known class of fountain codes
  with linear time encoding and decoding.

And RFC6330_:

  RaptorQ codes are a new family of codes that provide superior flexibility,
  support for larger source block sizes, and better coding efficiency than
  Raptor codes in RFC 5053.

  ... in most cases, a set of cardinality equal to the number of source symbols
  is sufficient; in rare cases, a set of cardinality slightly more than the
  number of source symbols is required.

Which in practice means that source data of size 1 MiB can be recovered from any
1.002 MiB of received data (from `"Application Layer Forward Error Correction
for Mobile Multimedia Broadcasting Case Study" paper`_).

.. _CFFI: http://cffi.readthedocs.org/
.. _libRaptorQ: https://github.com/LucaFulchir/libRaptorQ/
.. _RFC6330: https://tools.ietf.org/html/rfc6330
.. _wikipedia on Raptor code: https://en.wikipedia.org/wiki/Raptor_code
.. _"Application Layer Forward Error Correction for Mobile Multimedia Broadcasting Case Study" paper:
   https://www.qualcomm.com/media/documents/files/raptor-codes-for-mobile-multimedia-broadcasting-case-study.pdf


.. contents::
  :backlinks: none



Usage
-----

Module closely follows libRaptorQ_ API at the moment.

It includes command-line script ("rq", when installed or as symlink in the
repo), which has example code for both encoding and decoding, and can be used as
a standalone tool, or for basic algorithm testing/showcase.

Encode file, with 50% extra symbols (resulting data chunks to be stored/transmitted
on/over lossy medium) and 20% of total of these dropped (just for testing purposes)
before saving them to setup.py.enc::

  % ./rq --debug encode --repair-symbols-rate 0.5 --drop-rate 0.2 setup.py setup.py.enc
  2015-12-15 03:00:01 :: DEBUG :: Encoded 24 block(s),
    562 symbol(s) total (240 for repair). Dropped 141 symbol(s).

Decode resulting file back from this::

  % ./rq --debug decode setup.py.enc setup.py.dec
  2015-12-15 03:02:33 :: DEBUG :: Decoded 1849B of data from 562 symbols (total, discarded: 0)

  % sha256sum -b setup.py{,.dec}
  e73507cbf1422e1d53b10e72433989b8145b8946549104d6346219518221b6a1 *setup.py
  e73507cbf1422e1d53b10e72433989b8145b8946549104d6346219518221b6a1 *setup.py.dec

Output data ("setup.py.enc" in the example) for the script is JSON-encoded list
of base64-encoded symbols, as well as some parameters for lib init.

See output with --help option for all the other script parameters.

To use as a python module, do ``import libraptorq`` and roughly same things as
`__main__.py <https://github.com/mk-fg/python-libraptorq/blob/master/libraptorq/__main__.py>`_
does.

See also libRaptorQ_ docs for info on its API, which this module wraps around.



Installation
------------

It's a regular package for Python 2.7 (not 3.X).

It uses and needs CFFI_ and libRaptorQ_ installed on the system.

Using pip_ is the best way::

  % pip install libraptorq

If you don't have it, use::

  % easy_install pip
  % pip install libraptorq

Alternatively (see also `pip2014.com`_ and `pip install guide`_)::

  % curl https://raw.github.com/pypa/pip/master/contrib/get-pip.py | python
  % pip install libraptorq

Or, if you absolutely must::

  % easy_install libraptorq

But, you really shouldn't do that.

Current-git version can be installed like this::

  % pip install 'git+https://github.com/mk-fg/python-libraptorq.git#egg=libraptorq'

Note that to install stuff in system-wide PATH and site-packages, elevated
privileges are often required.
Use "install --user", `~/.pydistutils.cfg`_ or virtualenv_ to do unprivileged
installs into custom paths.

Alternatively, `./rq` tool can be run right from the checkout tree without any
installation, if that's the only thing you need there.

.. _pip: http://pip-installer.org/
.. _pip2014.com: http://pip2014.com/
.. _pip install guide: http://www.pip-installer.org/en/latest/installing.html
.. _~/.pydistutils.cfg: http://docs.python.org/install/index.html#distutils-configuration-files
.. _virtualenv: http://pypi.python.org/pypi/virtualenv