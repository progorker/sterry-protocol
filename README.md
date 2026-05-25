```
=====_=====================
  __| |_ ___ _ _ _ _ _  _ 
 (_-<  _/ -_) '_| '_| || |
 /__/\__\___|_| |_|  \_, |
=====================|__/==
     Sterry Protocol
===========================

Sterry is a communication protocol for distributing,
searching, and retrieving documents in Internet
Protocol networks. The design of the Sterry protocol
and user interface is menu-driven which is derived &
improved from Gopher protocol.

---------------------|__/--
       User Request
---------------------------

First, the client establishes a TCP connection with
the server on port 311, the standard sterry port.
The client then sends a string ended with two "CR + LF"
sequence. This is the selector, which identifies the
document to be retrieved. The selector includes:

-----
server name / ip<cr+lf>
server port<cr+lf>
auth token<cr+lf>
document path<cr+lf><cr+lf>
-----

---------------------|__/--
     Server Response
---------------------------

The server then replies with the requested item and closes
the connection. The response includes:

-----
response type<cr+lf>
filename<cr+lf>
data / menu size<cr+lf>.<cr+lf>
data / menu
-----

+ Response type:
  o 'm': menu
  o 't': text data
  o 'b': binary data

```
