bson Reference
==============================================================================

The following are the references for bson.



Types
=====



.. _Bson.type:
Bson
---------------------------------------------------------

    .. code:: nim

        Bson* = ref object of RootObj  ## Bson Node
          case kind*: BsonKind
          of BsonKindDouble:           valueFloat64:     float64
          of BsonKindStringUTF8:       valueString:      string
          of BsonKindDocument:         valueDocument:    OrderedTable[string, Bson]
          of BsonKindArray:            valueArray:       seq[Bson]
          of BsonKindBinary:
          of BsonKindUndefined:        discard
          of BsonKindOid:              valueOid:         Oid
          of BsonKindBool:             valueBool:        bool
          of BsonKindTimeUTC:          valueTime:        Time
          of BsonKindNull:             discard
          of BsonKindRegexp:
          of BsonKindDBPointer:
          of BsonKindJSCode:           valueCode:        string
          of BsonKindDeprecated:       valueDepr:        string
          of BsonKindJSCodeWithScope:  valueCodeWS:      string
          of BsonKindInt32:            valueInt32:       int32
          of BsonKindTimestamp:        valueTimestamp:   BsonTimestamp
          of BsonKindInt64:            valueInt64:       int64
          of BsonKindMaximumKey:       discard
          of BsonKindMinimumKey:       discard
          else:                        discard


    source line: `239 <../src/bson.nim#L239>`__



.. _BsonTimestamp.type:
BsonTimestamp
---------------------------------------------------------

    .. code:: nim

        BsonTimestamp* = object ## Internal MongoDB type used by mongos instances
          increment*: int32
          timestamp*: int32


    source line: `235 <../src/bson.nim#L235>`__







Procs, Methods, Iterators
=========================


.. _`$`.p:
`$`
---------------------------------------------------------

    .. code:: nim

        proc `$`*(bs: Bson): string =

    source line: `283 <../src/bson.nim#L283>`__

    Serialize the ``bs`` Bson object into a human readable string.
    
    While the generated string is visually similar to JSON, it is NOT an
    accurate rendition of JSON. It is instead simply a convenient reference
    mostly used for diagnostics.


.. _`[]=`.p:
`[]=`
---------------------------------------------------------

    .. code:: nim

        proc `[]=`*(bs: Bson, key: int, value: Bson) =

    source line: `830 <../src/bson.nim#L830>`__

    Modify Bson object array element at index ``key`` with ``value``.
    
    The converters will be tried if ``value`` is not of type ``Bson``.
    
    So, for example:
    
    .. code:: nim
    
        myArray[3] = toBson("c")
    
    is effectively the same as:
    
        myArray[3] = "c"
    
    If the item is out of range, an exception is raised.


.. _`[]=`.p:
`[]=`
---------------------------------------------------------

    .. code:: nim

        proc `[]=`*(bs: Bson, key: string, value: Bson) =

    source line: `808 <../src/bson.nim#L808>`__

    Modify Bson object document field at ``key`` with a Bson object ``value``.
    
    If the field is not found, an exception is raised.


.. _`[]=`.p:
`[]=`
---------------------------------------------------------

    .. code:: nim

        proc `[]=`*(bs: Bson, key: string, value: Oid) =
        proc `[]=`*(bs: Bson, key: string, value: float64) = 
        proc `[]=`*(bs: Bson, key: string, value: string) =  
        proc `[]=`*(bs: Bson, key: string, value: int64) =  
        proc `[]=`*(bs: Bson, key: string, value: int32) =  
        proc `[]=`*(bs: Bson, key: string, value: int) =  
        proc `[]=`*(bs: Bson, key: string, value: bool) =  
        proc `[]=`*(bs: Bson, key: string, value: Time) =  

    source line: `439 <../src/bson.nim#L439>`__

    Modify BSON document field with an explicit value of a native/std Nim type.
    
    If setting an ``Oid`` and the Object ID is all-zeroes ("000000000000000000000000"), then
    a null field is stored rather than an Object ID value
    
    If the field does ot exist, an exception is raised.
    
    Returns a Bson object.


.. _`[]`.p:
`[]`
---------------------------------------------------------

    .. code:: nim

        proc `[]`*(bs: Bson, key: int): Bson =

    source line: `818 <../src/bson.nim#L818>`__

    Get BSON array item at index ``key``.
    
    If the item is out of range, an exception is raised.
    
    Returns a Bson object.


.. _`[]`.p:
`[]`
---------------------------------------------------------

    .. code:: nim

        proc `[]`*(bs: Bson, key: string): Bson =

    source line: `800 <../src/bson.nim#L800>`__

    Get BSON object field


.. _`{}=`.p:
`{}=`
---------------------------------------------------------

    .. code:: nim

        proc `{}=`*(bs: Bson, keys: varargs[string], value: Bson) =

    source line: `1463 <../src/bson.nim#L1463>`__

    Set a Bson object from a Bson document or array in a forgiving manner.
    Calling this procedure should never generate an exception.
    
    If the key (or keys) do not exist, they are automatically created.
    
    If a Bson object (or sub-object) is an array, the ``key`` string is converted
    to an int.
    
    Examples:
    
    .. code:: nim
    
        let myDoc = @@{"abc": 4, "xyz": {"foo": "bar", "zip": [10, 11, 12, 13]}}
    
        myDoc{"abc"} = toBson(5)
        myDoc{"xyz", "foo"} = toBson("BAR2")
        myDoc{"def", "ghi"} = toBson(99.2)
        myDoc{"xyz", "zip", "2"} = toBson(112)
    
        assert myDoc["abc"] == 5
        assert myDoc["xyz"]["foo"] == "BAR2"
        assert myDoc["def"]["ghi"] == 99.2
        assert myDoc["xyz"]["zip"][2] == 112
    


.. _`{}=`.p:
`{}=`
---------------------------------------------------------

    .. code:: nim

        proc `{}=`*[T](bs: Bson, keys: varargs[string], value: T) =

    source line: `1526 <../src/bson.nim#L1526>`__

    Set a Bson object from a Bson document or array in a forgiving manner using
    a known convertable nim type.
    
    Calling this procedure should never generate an exception unless the type
    not known at compile time.
    
    If the key (or keys) do not exist, they are automatically created.
    
    If a Bson object (or sub-object) is an array, the ``key`` string is converted
    to an int.
    
    Examples:
    
    .. code:: nim
    
        let myDoc = @@{"abc": 4, "xyz": {"foo": "bar", "zip": [10, 11, 12, 13]}}
    
        # we DON'T have to use "toBson(x)" for types that have such a procedure defined.
    
        myDoc{"abc"} = 5
        myDoc{"xyz", "foo"} = "BAR2"
        myDoc{"def", "ghi"} = 99.2
        myDoc{"xyz", "zip", "2"} = 112
    
        assert myDoc["abc"] == 5
        assert myDoc["xyz"]["foo"] == "BAR2"
        assert myDoc["def"]["ghi"] == 99.2
        assert myDoc["xyz"]["zip"][2] == 112
    


.. _`{}`.p:
`{}`
---------------------------------------------------------

    .. code:: nim

        proc `{}`*(bs: Bson, keys: varargs[string]): Bson =

    source line: `1420 <../src/bson.nim#L1420>`__

    Get a Bson object from a Bson document or array in a forgiving manner.
    Calling this procedure should never generate an exception.
    
    If the key (or key sequence) exists, then the corresponding object is
    returned.
    
    If the Bson object (or sub-object) is an array, the ``key`` string is converted
    to an int.
    
    If it does not exist, then a ``null`` is returned.
    
    Examples:
    
    .. code:: nim
    
        let myDoc = @@{"abc": 4, "xyz": {"foo": "bar", "zip": [10, 11, 12, 13]}}
    
        assert myDoc{"abc"} == 4
        assert myDoc{"missing"}.isNull()
        assert myDoc{"xyz", "foo"} == "bar"
        assert myDoc{"xyz", "zip", "2"} == 12
        assert myDoc{"xyz", "zip", "22"}.isNull()
    


.. _add.p:
add
---------------------------------------------------------

    .. code:: nim

        proc add*[T](bs: Bson, value: T): Bson {.discardable.} =

    source line: `1050 <../src/bson.nim#L1050>`__

    Add a new BSON item to the the array's list.
    
    It both returns a new BSON array and modifies the original in-place.


.. _bin.p:
bin
---------------------------------------------------------

    .. code:: nim

        proc bin*(bindata: string): Bson =

    source line: `976 <../src/bson.nim#L976>`__

    Create new binary Bson object with ``generic`` subtype
    
    To convert it back to a "binary string", use ``binstr``.
    
    Returns a new BSON object.


.. _binstr.p:
binstr
---------------------------------------------------------

    .. code:: nim

        proc binstr*(x: Bson): string =

    source line: `988 <../src/bson.nim#L988>`__

    Generate a "binary string" equivalent of the BSON "Generic Binary" field type.
    
    This is used strictly for that field type. If you are wanting to
    convert a BSON object into it's true binary form, use ``bytes`` instead.


.. _binuser.p:
binuser
---------------------------------------------------------

    .. code:: nim

        proc binuser*(bindata: string): Bson =

    source line: `1006 <../src/bson.nim#L1006>`__

    Create new binary BSON object with "user-defined" subtype.
    
    Returns a new BSON object.


.. _bytes.p:
bytes
---------------------------------------------------------

    .. code:: nim

        proc bytes*(bs: Bson): string =

    source line: `760 <../src/bson.nim#L760>`__

    Serialize a BSON document into the raw bytes.
    
    This procedure is used for generating the final binary document format
    that is BSON.
    
    While it is possible to run ``bytes`` agains any Bson object, it is generally
    used with the whole document.
    
    If you are wanting to get the content of a binary field (aka BinData), see
    the ``binstr`` function instead.
    
    Returns a binary string (not generally printable).


.. _contains.p:
contains
---------------------------------------------------------

    .. code:: nim

        proc contains*(bs: Bson, key: string): bool =

    source line: `1132 <../src/bson.nim#L1132>`__

    Check if Bson document has a specified field.
    
    Returns ``true`` if found, ``false`` otherwise.
    If the ``bs`` object is not a document, then it returns ``false``.


.. _dbref.p:
dbref
---------------------------------------------------------

    .. code:: nim

        proc dbref*(refCollection: string, refOid: Oid): Bson =

    source line: `912 <../src/bson.nim#L912>`__

    Create a new DBRef (database reference) MongoDB bson type
    
    refCollection
      the name of the collection being referenced
    
    refOid
      the ``_id`` of the document sitting in the collection
    
    Returns a new BSON object.


.. _del.p:
del
---------------------------------------------------------

    .. code:: nim

        proc del*(bs: Bson, key: string) =
        proc del*(bs: Bson, idx: int) =  
        proc delete*(bs: Bson, key: string) =  
        proc delete*(bs: Bson, idx: int) =  

    source line: `1058 <../src/bson.nim#L1058>`__

    Deletes a field from a BSON object or array.
    
    If passed a string, it removes a field from an object.
    If passed an integer, it removes an item by index from an array
    
    This procedure modifies the object that is passed to it.


.. _fields.i:
fields
---------------------------------------------------------

    .. code:: nim

        iterator fields*(bs: Bson): string =

    source line: `1109 <../src/bson.nim#L1109>`__

    Iterate over BSON document's field name(s).
    
    If the ``bs`` object is not a document, an exception is thrown.
    
    Each call returns one BSON field.


.. _geo.p:
geo
---------------------------------------------------------

    .. code:: nim

        proc geo*(loc: GeoPoint): Bson =

    source line: `1016 <../src/bson.nim#L1016>`__

    Convert array of two floats into Bson as a Geo-Point.
    
    Returns a new BSON object.


.. _isNull.p:
isNull
---------------------------------------------------------

    .. code:: nim

        proc isNull*(bs: Bson): bool =

    source line: `939 <../src/bson.nim#L939>`__

    Checks to see if the Bson object is of type ``null``.


.. _items.i:
items
---------------------------------------------------------

    .. code:: nim

        iterator items*(bs: Bson): Bson =

    source line: `1092 <../src/bson.nim#L1092>`__

    Iterate over BSON document's values or an array's items.
    
    If ``bs`` is not a document or array, an exception is thrown.
    
    Each call returns one BSON item/value.


.. _js.p:
js
---------------------------------------------------------

    .. code:: nim

        proc js*(code: string): Bson =

    source line: `970 <../src/bson.nim#L970>`__

    Create new Bson object representing JavaScript code.
    
    Returns a new BSON object.


.. _len.p:
len
---------------------------------------------------------

    .. code:: nim

        proc len*(bs: Bson):int =

    source line: `1036 <../src/bson.nim#L1036>`__

    Get the length of an array or the number of fields in a document.
    
    If not an array or document, an exception is generated.
    
    Returns the length as an integer.


.. _maxkey.p:
maxkey
---------------------------------------------------------

    .. code:: nim

        proc maxkey*(): Bson =

    source line: `956 <../src/bson.nim#L956>`__

    Create new BSON object representing 'Max key' BSON type.
    
    Returns a new BSON object.


.. _merge.p:
merge
---------------------------------------------------------

    .. code:: nim

        proc merge*(a, b: Bson): Bson =

    source line: `1259 <../src/bson.nim#L1259>`__

    Combine two BSON documents into a new one.
    
    The resulting document contains all the fields of both.
    If both ``a`` and ``b`` contain the same field, the
    value in ``b`` is used.
    
    For example:
    
    
    .. code:: nim
    
        let a = @@{"name": "Joe", "age": 42, weight: 50 }
        let b = @@{"name": "Joe", "feet": 2, weight: 52 }
        let both = a.merge(b)
        echo $both
    
    displays
    
    .. code:: json
    
        {
            "name" : "Joe",
            "age" : 42,
            "weight" : 52,
            "feet" : 2
        }
    
    Also see the related procedure called ``update(a, b)``.
    
    Returns a combined BSON document object.


.. _minkey.p:
minkey
---------------------------------------------------------

    .. code:: nim

        proc minkey*(): Bson =

    source line: `949 <../src/bson.nim#L949>`__

    Create new BSON object representing 'Min key' BSON type.
    
    Returns a new BSON object.


.. _newBsonArray.p:
newBsonArray
---------------------------------------------------------

    .. code:: nim

        proc newBsonArray*(): Bson =

    source line: `792 <../src/bson.nim#L792>`__

    Create new Bson array


.. _newBsonDocument.p:
newBsonDocument
---------------------------------------------------------

    .. code:: nim

        proc newBsonDocument*(): Bson =

    source line: `784 <../src/bson.nim#L784>`__

    Create new empty Bson document.
    
    Returns a new Bson object.


.. _newBsonDocument.p:
newBsonDocument
---------------------------------------------------------

    .. code:: nim

        proc newBsonDocument*(bytes: string): Bson =

    source line: `1249 <../src/bson.nim#L1249>`__

    Create new Bson document from a byte string
    formatted to the BSON specification.


.. _newBsonDocument.p:
newBsonDocument
---------------------------------------------------------

    .. code:: nim

        proc newBsonDocument*(s: Stream): Bson =

    source line: `1150 <../src/bson.nim#L1150>`__

    Create new Bson document from a byte stream formatted to the BSON
    specifications.


.. _notNull.p:
notNull
---------------------------------------------------------

    .. code:: nim

        proc notNull*(bs: Bson): bool =

    source line: `944 <../src/bson.nim#L944>`__

    Checks to see if the Bson object is NOT of type ``null``.


.. _null.p:
null
---------------------------------------------------------

    .. code:: nim

        proc null*(): Bson =

    source line: `932 <../src/bson.nim#L932>`__

    Create new BSON 'null' value
    
    Returns a new BSON object.


.. _pairs.i:
pairs
---------------------------------------------------------

    .. code:: nim

        iterator pairs*(bs: Bson): tuple[key: string, val: Bson] =

    source line: `1123 <../src/bson.nim#L1123>`__

    Iterate over BSON object's fields
    
    Each call returns one (key, value) tuple.


.. _pull.p:
pull
---------------------------------------------------------

    .. code:: nim

        proc pull*(a: var Bson, b: Bson)=

    source line: `1324 <../src/bson.nim#L1324>`__

    Modifies the content of document ``a`` with the updated content of document ``b``.
    
    If ``a`` and ``b`` contain the same field, the value in ``b`` is set
    in ``a``.
    
    If ``b`` has a field not contained in ``a``, it is skipped.
    
    Works with both documents and arrays. With anything else, nothing happens.
    
    Examples:
    
    .. code:: nim
    
        var a = @@{"abc": 4, "xyz": {"foo": "bar", "zip": [10, 11, 12, 13]}}
        let b = @@{"abc": 2, "xyz": {"foo": "tada", "j": "u"}}
        let c = @@{"abc": "hello"}
        let d = @@{"zip": [0.1, 0.2, 0.3]}
    
        a.pull(b)
        assert a["abc"] == 2
        assert a["xyz"]["foo"] == "tada"
        assert a{"xyz", "j"}.isNull       # "j" is not set because it is not found in ``a``
        assert a["xyz"]["zip"].len == 4   # "zip" is left alone
    
        a.pull(c)
        assert a["abc"] == "hello"
    
        a["xyz"].pull(d)
        assert a["xyz"]["zip"][0] == 0.1
        assert a["xyz"]["zip"][1] == 0.2
        assert a["xyz"]["zip"][2] == 0.3
        assert a["xyz"]["zip"][3] == 13
    
    Also see the related procedure called ``merge(a, b)``.


.. _regex.p:
regex
---------------------------------------------------------

    .. code:: nim

        proc regex*(pattern: string, options: string): Bson =

    source line: `963 <../src/bson.nim#L963>`__

    Create new Bson value representing Regexp BSON type
    
    Returns a new BSON object.


.. _timeUTC.p:
timeUTC
---------------------------------------------------------

    .. code:: nim

        proc timeUTC*(time: Time): Bson =

    source line: `1026 <../src/bson.nim#L1026>`__

    Create UTC datetime BSON object.
    
    Returns a new BSON object.


.. _toBson.p:
toBson
---------------------------------------------------------

    .. code:: nim

        proc toBson*(x: NimNode): NimNode {.compileTime.} =

    source line: `868 <../src/bson.nim#L868>`__



.. _toBson.p:
toBson
---------------------------------------------------------

    .. code:: nim

        proc toBson*(x: Oid): Bson =
        proc toBson*(x: float64): Bson = 
        proc toBson*(x: string): Bson =  
        proc toBson*(x: int64): Bson =   
        proc toBson*(x: int32): Bson =   
        proc toBson*(x: int): Bson =   
        proc toBson*(x: bool): Bson =   
        proc toBson*(x: Time): Bson =  
        proc toBson*(x: BsonTimestamp): Bson =  
        proc toBson*(x: MD5Digest): Bson =  
        proc toBson*(x: var MD5Context): Bson = 
        proc toBson*(keyVals: openArray[tuple[key: string, val: Bson]]): Bson =  
        proc toBson*[T](vals: openArray[T]): Bson =  

    source line: `383 <../src/bson.nim#L383>`__

    Convert nim data types to the corresponding Bson object.
    
    For ``Oid``, see the ``oids`` standard Nim library.
    If the oid is all-zeroes ("000000000000000000000000"), then
    a null is created rather than an ObjectID value
    
    For ``Time``, see the ``times`` standard Nim library.
    
    For ``MD5Digest`` or ``MD5Context``, see the ``md5`` standard Nim library.
    Calling ``toBson`` on a ``MD5Context`` finalizes it during the conversion.
    
    For ``BsonTimestamp``, see the internal data types set up in this library.
    
    An array of (``string``, ``Bson``) tuples is converted into the corresponding
    Bson document.
    
    A simple array of any type that has a ``toBson`` proc is converted into the
    corresponding Bson object array (``BsonKindArray``).
    
    Returns a Bson object.


.. _undefined.p:
undefined
---------------------------------------------------------

    .. code:: nim

        proc undefined*(): Bson =

    source line: `925 <../src/bson.nim#L925>`__

    Create new Bson "undefined" (``BsonKindUndefined``) object.
    
    Returns a new BSON object.


.. _update.p:
update
---------------------------------------------------------

    .. code:: nim

        proc update*(a: var Bson, b: Bson) = pull(a, b)

    source line: `1416 <../src/bson.nim#L1416>`__

    deprecated name




Converters
==========


.. _toBool.c:
toBool
---------------------------------------------------------

    .. code:: nim

        

    source line: `577 <../src/bson.nim#L577>`__

    Convert Bson object to bool


.. _toBsonKind.c:
toBsonKind
---------------------------------------------------------

    .. code:: nim

        

    source line: `228 <../src/bson.nim#L228>`__

    Convert char to BsonKind


.. _toChar.c:
toChar
---------------------------------------------------------

    .. code:: nim

        

    source line: `220 <../src/bson.nim#L220>`__

    Convert BsonKind to char


.. _toChar.c:
toChar
---------------------------------------------------------

    .. code:: nim

        

    source line: `224 <../src/bson.nim#L224>`__

    Convert BsonSubtype to char


.. _toFloat64.c:
toFloat64
---------------------------------------------------------

    .. code:: nim

        

    source line: `461 <../src/bson.nim#L461>`__

    Convert Bson object to float64


.. _toInt.c:
toInt
---------------------------------------------------------

    .. code:: nim

        

    source line: `548 <../src/bson.nim#L548>`__

    Convert Bson to int whether it is int32 or int64


.. _toInt32.c:
toInt32
---------------------------------------------------------

    .. code:: nim

        

    source line: `527 <../src/bson.nim#L527>`__

    Convert Bson to int32


.. _toInt64.c:
toInt64
---------------------------------------------------------

    .. code:: nim

        

    source line: `502 <../src/bson.nim#L502>`__

    Convert Bson object to int64


.. _toOid.c:
toOid
---------------------------------------------------------

    .. code:: nim

        

    source line: `409 <../src/bson.nim#L409>`__

    Convert Bson to Mongo Object ID
    
    If ``x`` is a ``null``, then the all-zeroes Oid is returned.
    
    If ``x`` is a real Oid, then that value is returned.
    
    If ``x`` is a BSON string, then an attempt is made to parse it to an Oid.
    
    If ``x`` is a BSON document and there is a field called "$oid", then an attempt is made to parse that field's value to an Oid.
    
    Otherwise, the all-zeroes Oid is returned.


.. _toString.c:
toString
---------------------------------------------------------

    .. code:: nim

        

    source line: `480 <../src/bson.nim#L480>`__

    Convert Bson to UTF8 string


.. _toTime.c:
toTime
---------------------------------------------------------

    .. code:: nim

        

    source line: `603 <../src/bson.nim#L603>`__

    Convert Bson object to Time.
    
    Only works with the BSON Date (``BsonKindTimeUTC``).


.. _toTimestamp.c:
toTimestamp
---------------------------------------------------------

    .. code:: nim

        

    source line: `624 <../src/bson.nim#L624>`__

    Convert Bson object to a BsonTimestamp type
    
    Please note that BSON timestamp is really only meant to be used
    by the MongoDB database for "internal use only".
    
    If you are wanting to store time, use the "date" aka ``BsonKindTimeUTC``
    objects instead.




Macros and Templates
====================


.. _`@@`.m:
`@@`
---------------------------------------------------------

    .. code:: nim

        macro `@@`*(x: untyped): Bson =

    source line: `894 <../src/bson.nim#L894>`__

    Convert a *table constructor* (at compile-time) into a Bson document
    
    Example:
    
    .. code:: nim
    
        let a = @@{"name": "Joe", "age": 42, "weight": 50.3}
    
        assert a["name"] == "Joe"
        assert a["age"] == 42
    
    Despite the appearance, a table constructor is NOT JSON. It is a
    means of expressing a table of dynamic elements for resolution at
    compile-time.


.. _toBson.t:
toBson
---------------------------------------------------------

    .. code:: nim

        template toBson*(b: Bson): Bson = b

    source line: `863 <../src/bson.nim#L863>`__

    This template converts Bson into itself... Bson.
    Having this template helps catch border cases internally; especially with macros.





Table Of Contents
=================

1. `Introduction to bson <https://github.com/JohnAD/bson>`__
2. Appendices

    A. `bson Reference <bson-ref.rst>`__
    B. `bson/marshal Reference <bson-marshal-ref.rst>`__
