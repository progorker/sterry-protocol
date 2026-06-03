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
json text data<cr+lf>
document path<cr+lf><cr+lf>
-----

'json text data' is escaped as following:
---
drop function if exists sterry_escape;
delimiter $$
create function sterry_escape( p_input varchar(8192) )
returns varchar(8192)
deterministic
sql security invoker
begin
  declare v_output varchar(8192);
  if p_input is null then
    set v_output = 'NULL';
  else
    set v_output = p_input;
  end if;
  set v_output = replace( v_output, '''', '__sq__' );
  set v_output = replace( v_output, '\"', '__dq__' );
  set v_output = replace( v_output, '\n', '__nl__' );
  set v_output = replace( v_output, '\r', '__cr__' );
  set v_output = replace( v_output, '\t', '__tb__' );
  set v_output = replace( v_output, '\\', '__sl__' );
  return v_output;
end;$$
delimiter ;
---
drop function if exists sterry_unescape;
delimiter $$
create function sterry_unescape( p_input varchar(8192) )
returns varchar(8192)
deterministic
sql security invoker
begin
  declare v_output varchar(8192);
  if p_input is null then
    set v_output = 'NULL';
  else
    set v_output = p_input;
  end if;
  set v_output = replace( v_output, '__sq__', '''' );
  set v_output = replace( v_output, '__dq__', '\"' );
  set v_output = replace( v_output, '__nl__', '\n' );
  set v_output = replace( v_output, '__cr__', '\r' );
  set v_output = replace( v_output, '__tb__', '\t' );
  set v_output = replace( v_output, '__sl__', '\\' );
  return v_output;
end;$$
delimiter ;
---

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
  o 'j': json text data
  o 'b': binary data


---------------------|__/--
      Menu Format
---------------------------

Sterry menu items are defined by lines of tab-separated
values in a text file. This file can be called a sterrymap.
Each tab-seperated line (called a selector line) gives
the client software a description of the menu item: what
it is, what it is called, and where it leads to. The client
displays the menu items in the order that they appear in the
sterrymap.

The first two characters in a selector line indicates the
item type, which tells the client what kind of file or protocol
the menu item points to. This helps the client decide what to
do with it.

The item type is followed by the user display string (a description
or label that represents the item in the menu; the selector (a path
or other string for the resource on the server); the hostname (the
domain name or IP address of the server; the network port; the auth
token and json text data as parameters.

All lines in a sterry menu are terminated by "CR+LF".

+------+----------------------------------------------------------+
| type | description                                              |
+------+----------------------------------------------------------+
| mi   | Informational message, widely used                       |
| mm   | Menu file                                                |
| mt   | Text file                                                |
| mj   | Json text file                                           |
| mb   | Binary file                                              |
| mh   | HTML file                                                |
| mp   | Image file (common)                                      |
| mv   | Movie file (common)                                      |
| ms   | Sound file (common)                                      |
| md   | Documentation file (common)                              |
| me   | Embedded file (common)                                   |
| pg   | Image file (.gif)                                        |
| pb   | Image file (.bmp)                                        |
| pn   | Image file (.png)                                        |
| v4   | Movie file (.mp4)                                        |
| s3   | Sound file (.mp3)                                        |
| dp   | Documentation file (.pdf)                                |
| dw   | Documentation file (.doc, .docx)                         |
| dx   | Documentation file (.xls, .xlsx)                         |
| dr   | Documentation file (.ppt, .pptx)                         |
| dl   | Documentation file (.xml)                                |
| ey   | Embedded file (YouTube)                                  |
| ed   | Embedded file (DailyMotion)                              |
| eh   | Embedded file (HTML)                                     |
| es   | Embedded file (Server-side scripts)                      |
+------+----------------------------------------------------------+

```
