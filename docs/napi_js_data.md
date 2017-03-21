# NAPI - JavaScript Data Types and Values 

NAPI exposes a set of APIs to create fundamental JavaScript Objects. 
Some of these types are documented under [Section 6](https://tc39.github.io/ecma262/#sec-ecmascript-data-types-and-values) 
of the [ECMAScript Language Specification](https://tc39.github.io/ecma262/).

 Fundamentally, these APIs are used to do one of the following:
1. Create a new JavaScript object represented by `napi_value`
2. Convert from a primitive C++ type to `napi_value`
3. Convert from `napi_value` to a primitive C++ type

## Enum types

### *napi_value_type*

#### Definition
```
enum napi_valuetype {
  napi_undefined,
  napi_null,
  napi_boolean,
  napi_number,
  napi_string,
  napi_symbol,
  napi_object,
  napi_function,
  napi_external,
};
```

#### Description
Describes the type of a napi_value. This generally corresponds with the types described
in [Section 6.1 of the ECMAScript Language Specification](https://tc39.github.io/ecma262/#sec-ecmascript-language-types).
In addition to types in that section, `napi_value_type` also can represent Functions and Objects with external data.

### *napi_typedarray_type*

#### Definition
```
enum napi_typedarray_type {
  napi_int8,
  napi_uint8,
  napi_uint8_clamped,
  napi_int16,
  napi_uint16,
  napi_int32,
  napi_uint32,
  napi_float32,
  napi_float64,
};
```

#### Description
This represents the underlying binary scalar datatype of the TypedArray.
Elements of this enum correspond to [Section 22.2 of the ECMAScript Language Specification](https://tc39.github.io/ecma262/#sec-typedarray-objects).

## Functions

### *napi_create_array*

#### Signature
```
napi_status napi_create_array(napi_env e, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the NAPI API is invoked under
- `[out] result`: A `napi_value` representing a JavaScript Array

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API returns a NAPI value corresponding to a JavaScript Array type. 
JavaScript arrays are described in [Section 22.1](https://tc39.github.io/ecma262/#sec-array-objects) 
of the ECMAScript Language Specification.

### *napi_create_array_with_length*

#### Signature
```
napi_status napi_create_array(napi_env e, int length, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  length`: The initial length of the Array
- `[out] result`: A `napi_value` representing a JavaScript Array

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API returns a NAPI value corresponding to a JavaScript Array type. 
The Array's length property is set to the passed in length parameter. 
However, the underlying buffer is not guaranteed to be pre-allocated- that behavior
is left to the underlying VM implementation. If you need the buffer to be 
guaranteed, consider using `napi_create_external_arraybuffer`.

JavaScript arrays are described in [Section 22.1](https://tc39.github.io/ecma262/#sec-array-objects) 
of the ECMAScript Language Specification.

### *napi_create_arraybuffer*

#### Signature
```
napi_status napi_create_arraybuffer(napi_env env, size_t byte_length, void** data, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  length`: The length in bytes of the array buffer to create
- `[out] data`: Pointer to the underlying byte buffer of the ArrayBuffer
- `[out] result`: A `napi_value` representing a JavaScript ArrayBuffer

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API returns a NAPI value corresponding to a JavaScript ArrayBuffer.
ArrayBuffers are used to represent fixed-length binary data buffers. 
The ArrayBuffer allocated will have an underlying byte buffer whose size is determined 
by the `length` parameter that's passed in. 
The underlying buffer is optionally returned back to the caller in case the caller
wants to directly manipulate the buffer. This buffer can only be written to directly
from Native code. To write to this buffer from JavaScript, a typed array or DataView
object would need to be created.

JavaScript ArrayBuffers are described in [Section 24.1](https://tc39.github.io/ecma262/#sec-arraybuffer-objects) 
of the ECMAScript Language Specification.

### *napi_create_boolean*

#### Signature
```
napi_status napi_create_boolean(napi_env e, bool b, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  b`: Boolean value to convert to a `napi_value`
- `[out] result`: A `napi_value` representing a JavaScript Boolean

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API returns a NAPI value corresponding to a JavaScript Boolean.
It's used to convert from C++ boolean types to the corresponding JavaScript representation.

JavaScript Booleans are described in [Section 19.3](https://tc39.github.io/ecma262/#sec-boolean-objects) 
of the ECMAScript Language Specification.

### *napi_create_buffer*

#### Signature
```
napi_status napi_create_buffer(napi_env e, size_t size, void** data, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  size`: Size in bytes of the underlying buffer
- `[out] data`: Raw pointer to the underlying buffer
- `[out] result`: A `napi_value` representing a node::Buffer

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API allocates a node::Buffer object. It exists for compat with older Node versions.
Consider using typed arrays instead.

### *napi_create_buffer_copy*

#### Signature
```
napi_status napi_create_buffer_copy(napi_env e, const void* data, size_t size, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  data`: Raw pointer to the underlying buffer to copy from
- `[in]  size`: Size in bytes of the input buffer (should be same as size of the new buffer)
- `[out] result`: A `napi_value` representing a node::Buffer

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API allocates a node::Buffer object, and initializes it with data copied from the 
passed in buffer. It exists for compat with older Node versions. Consider using typed 
arrays instead.

### *napi_create_external*

#### Signature
```
napi_status napi_create_external(napi_env e, void* data, napi_finalize finalize_cb, void* finalize_hint, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  data`: Raw pointer to the external data being wrapped
- `[in]  finalize_cb`: Optional callback to call when the wrapped object is being collected
- `[in]  finalize_hint`: Optional hint to pass to the finalize callback during collection.
- `[out] result`: A `napi_value` representing an external object.

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API allocates a JavaScript object with external data attached to it.
This is used to wrap native objects and project them into JavaScript.
The API allows the caller to pass in a finalize callback, in case the underlying native
resource needs to be cleaned up when the wrapper JavaScript object gets collected.

### *napi_create_external_arraybuffer*

#### Signature
```
napi_status
napi_create_external_arraybuffer(napi_env env,
                                 void* external_data,
                                 size_t byte_length,
                                 napi_finalize finalize_cb,
                                 void* finalize_hint,
                                 napi_value* result);
```

#### Parameters
- `[in]  env`: The environment that the API is invoked under
- `[in]  external_data`: Pointer to the underlying byte buffer of the ArrayBuffer
- `[in]  byte_length`: The length in bytes of the underlying buffer
- `[in]  finalize_cb`: Optional callback to call when the ArrayBuffer is being collected
- `[in]  finalize_hint`: Optional hint to pass to the finalize callback during collection.
- `[out] result`: A `napi_value` representing a JavaScript ArrayBuffer

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API returns a NAPI value corresponding to a JavaScript ArrayBuffer.
The underlying byte buffer of the ArrayBuffer is externally allocated and managed.
The called must ensure that the byte buffer must be valid until the finalize callback is called.

JavaScript ArrayBuffers are described in [Section 24.1](https://tc39.github.io/ecma262/#sec-arraybuffer-objects) 
of the ECMAScript Language Specification.

### *napi_create_external_buffer*

#### Signature
```
napi_status napi_create_external_buffer(napi_env e,
                                        size_t size,
                                        void* data,
                                        napi_finalize finalize_cb,
                                        void* finalize_hint,
                                        napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  size`: Size in bytes of the input buffer (should be same as size of the new buffer)
- `[in]  data`: Raw pointer to the underlying buffer to copy from
- `[in]  finalize_cb`: Optional callback to call when the ArrayBuffer is being collected
- `[in]  finalize_hint`: Optional hint to pass to the finalize callback during collection.
- `[out] result`: A `napi_value` representing a node::Buffer

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API allocates a node::Buffer object, and initializes it with data backed by the 
passed in buffer. It exists for compat with older Node versions. Consider using typed 
arrays instead.

### *napi_create_function*

#### Signature
```
napi_status napi_create_function(napi_env e,
                                 const char* utf8name,
                                 napi_callback cb,
                                 void* data,
                                 napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  utf8name`: A string representing the name of the function encoded as utf8
- `[in]  cb`: A function pointer to the native function to be invoked when the created function is invoked from JavaScript
- `[in]  data`: Optional arbitrary context data to be passed in to the callback when the function is invoked
- `[out] result`: A `napi_value` representing a JavaScript Function

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API returns a NAPI value corresponding to a JavaScript Function object.
It's used to wrap native functions so that they can be invoked from JavaScript.

JavaScript Functions are described in [Section 19.2](https://tc39.github.io/ecma262/#sec-function-objects) 
of the ECMAScript Language Specification.

### *napi_create_number*

#### Signature
```
napi_status napi_create_number(napi_env e, double val, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  val`: Double-precision value to be represented in JavaScript
- `[out] result`: A `napi_value` representing a JavaScript Number

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API is used to convert from C++ doubles to JavaScript Number.

JavaScript Number type is described in [Section 6.1.6](https://tc39.github.io/ecma262/#sec-ecmascript-language-types-number-type) 
of the ECMAScript Language Specification.

### *napi_create_object*

#### Signature
```
napi_status napi_create_object(napi_env e, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[out] result`: A `napi_value` representing a JavaScript Object

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API allocates a default JavaScript Object. 
It is the equivalent of doing `new Object()` in JavaScript.

JavaScript Object type is described in [Section 6.1.7](https://tc39.github.io/ecma262/#sec-object-type) 
of the ECMAScript Language Specification.

### *napi_create_string_utf16*

#### Signature
```
napi_status napi_create_string_utf16(napi_env e, const char16_t* s, int length, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  s`: Character buffer representing a UTF16-LE encoded string
- `[in]  length`: The length of the string in characters, or -1 if it is null-terminated.
- `[out] result`: A `napi_value` representing a JavaScript String

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API creates a JavaScript String object from a UTF16-LE encoded C++ string

JavaScript String type is described in [Section 6.1.4](https://tc39.github.io/ecma262/#sec-ecmascript-language-types-string-type) 
of the ECMAScript Language Specification.

### *napi_create_string_utf8*

#### Signature
```
napi_status napi_create_string_utf8(napi_env e, const char* s, int length, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  s`: Character buffer representing a utf8 encoded string
- `[in]  length`: The length of the string in characters, or -1 if it is null-terminated.
- `[out] result`: A `napi_value` representing a JavaScript String

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API creates a JavaScript String object from a utf8 encoded C++ string

JavaScript String type is described in [Section 6.1.4](https://tc39.github.io/ecma262/#sec-ecmascript-language-types-string-type) 
of the ECMAScript Language Specification.

### *napi_create_symbol*

#### Signature
```
napi_status napi_create_string_utf8(napi_env e, const char* s, napi_value* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  s`: Null-terminated character buffer representing a utf8 encoded string
- `[out] result`: A `napi_value` representing a JavaScript Symbol

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API creates a JavaScript Symbol object from a utf8 encoded C++ string

JavaScript Symbol type is described in [Section 19.4](https://tc39.github.io/ecma262/#sec-symbol-objects) 
of the ECMAScript Language Specification.

### *napi_create_typedarray*

#### Signature
```
napi_status napi_create_typedarray(napi_env e,
                                   napi_typedarray_type type,
                                   size_t length,
                                   napi_value arraybuffer,
                                   size_t byte_offset,
                                   napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  type`: Scalar datatype of the elements within the TypedArray
- `[in]  length`: Number of elements in the TypedArray
- `[in]  arraybuffer`: ArrayBuffer underlying the typed array
- `[in]  byte_offset`: The byte offset within the ArrayBuffer from which to start projecting the TypedArray
- `[out] result`: A `napi_value` representing a JavaScript TypedArray

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API creates a JavaScript TypedArray object over an existing ArrayBuffer.
TypedArray objects provide an array-like view over an underlying data buffer 
where each element has the same underlying binary scalar datatype.

It's required that (length * size_of_element) + byte_offset should be <= ArrayBuffer.ArrayBufferByteLength. 
If not, a RangeError exception is raised.

JavaScript TypedArray Objects are described in [Section 22.2](https://tc39.github.io/ecma262/#sec-typedarray-objects) 
of the ECMAScript Language Specification.

### *napi_get_array_length*

#### Signature
```
napi_status napi_get_array_length(napi_env e, napi_value value, uint32_t* result);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  value`: napi_value representing the JavaScript Array whose length is being queried
- `[out] result`: uint32 representing length of the array

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API returns the length of an array.

Array length is described in [Section 22.1.4.1](https://tc39.github.io/ecma262/#sec-properties-of-array-instances-length) 
of the ECMAScript Language Specification.

### *napi_get_arraybuffer_info*

#### Signature
```
napi_status napi_get_arraybuffer_info(napi_env env,
                                      napi_value arraybuffer,
                                      void** data,
                                      size_t* byte_length);
```

#### Parameters
- `[in]  env`: The environment that the API is invoked under
- `[in]  arraybuffer`: napi_value representing the ArrayBuffer being queried
- `[out] data`: The underlying data buffer of the ArrayBuffer
- `[out] byte_length`: Length in bytes of the underlying data buffer

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API is used to retrieve the underlying data buffer of an ArrayBuffer and it's length.
WARNING: This API is dangerous. The lifetime of the underlying data buffer is managed by the 
ArrayBuffer even after it's returned to you. The one possible safe to use this API is in conjunction 
with napi_create_reference, which can be used to guarantee that you control the lifetime of
the ArrayBuffer.

<TODO: What happens to this buffer in case of a compacting GC?>

### *napi_get_buffer_info*

#### Signature
```
napi_status napi_get_buffer_info(napi_env e,
                                 napi_value value,
                                 void** data,
                                 size_t* length);
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  value`: napi_value representing the node::Buffer being queried
- `[out] data`: The underlying data buffer of the node::Buffer
- `[out] length`: Length in bytes of the underlying data buffer

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API is used to retrieve the underlying data buffer of an node::Buffer and it's length.

### *napi_get_false*

#### Signature
```
napi_status napi_get_false(napi_env e, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[out] result`: `napi_value` representing JavaScript Boolean false

#### Return value
- `napi_ok` if the API succeeded.

### *napi_get_global*

#### Signature
```
napi_status napi_get_global(napi_env e, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[out] result`: `napi_value` representing JavaScript Global Object

#### Return value
- `napi_ok` if the API succeeded.

### *napi_get_null*

#### Signature
```
napi_status napi_get_null(napi_env e, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[out] result`: `napi_value` representing JavaScript Null Object

#### Return value
- `napi_ok` if the API succeeded.

### *napi_get_prototype*

#### Signature
```
napi_status napi_get_prototype(napi_env e, napi_value o, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  o`: `napi_value` representing JavaScript Object whose prototype to return
- `[out] result`: `napi_value` representing prototype of the given object

#### Return value
- `napi_ok` if the API succeeded.

### *napi_get_true*

#### Signature
```
napi_status napi_get_true(napi_env e, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[out] result`: `napi_value` representing JavaScript Boolean true

#### Return value
- `napi_ok` if the API succeeded.

### *napi_get_typedarray_info*

#### Signature
```
napi_status napi_get_typedarray_info(napi_env e,
                                     napi_value typedarray,
                                     napi_typedarray_type* type,
                                     size_t* length,
                                     void** data,
                                     napi_value* arraybuffer,
                                     size_t* byte_offset)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  typedarray`: `napi_value` representing the TypedArray whose properties to query
- `[out] type`: Scalar datatype of the elements within the TypedArray
- `[out] length`: Number of elements in the TypedArray
- `[out] data`: The data buffer underlying the typed array
- `[out] byte_offset`: The byte offset within the data buffer from which to start projecting the TypedArray

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This API returns various properties of a typed array. 
Warning: This API is dangerous since the underlying data buffer is managed by the VM

### *napi_get_undefined*

#### Signature
```
napi_status napi_get_undefined(napi_env e, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[out] result`: `napi_value` representing JavaScript Undefined Object

#### Return value
- `napi_ok` if the API succeeded.

### *napi_get_value_bool*

#### Signature
```
napi_status napi_get_value_bool(napi_env e, napi_value v, bool* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: `napi_value` representing JavaScript Boolean
- `[out] result`: C++ boolean primitive equivalent of the given JavaScript Boolean

#### Return value
- `napi_ok` if the API succeeded.
- `napi_boolean_expected` if a non-boolean `napi_value` is passed in

### *napi_get_value_double*

#### Signature
```
napi_status napi_get_value_double(napi_env e, napi_value v, double* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: `napi_value` representing JavaScript Number
- `[out] result`: C++ double primitive equivalent of the given JavaScript Number

#### Return value
- `napi_ok` if the API succeeded.
- `napi_number_expected` if a non-number `napi_value` is passed in

### *napi_get_value_external*

#### Signature
```
napi_status napi_get_value_double(napi_env e, napi_value v, void** result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: `napi_value` representing JavaScript External Object
- `[out] result`: Pointer to the data wrapped by the JavaScript External Object

#### Return value
- `napi_ok` if the API succeeded.
- `napi_invalid_arg` if a non-external `napi_value` is passed in

### *napi_get_value_int32*

#### Signature
```
napi_status napi_get_value_int32(napi_env e, napi_value v, int32_t* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: `napi_value` representing JavaScript Number
- `[out] result`: C++ int32 primitive equivalent of the given JavaScript Number

#### Return value
- `napi_ok` if the API succeeded.
- `napi_number_expected` if a non-number `napi_value` is passed in

### *napi_get_value_int64*

#### Signature
```
napi_status napi_get_value_int64(napi_env e, napi_value v, int64_t* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: `napi_value` representing JavaScript Number
- `[out] result`: C++ int64 primitive equivalent of the given JavaScript Number

#### Return value
- `napi_ok` if the API succeeded.
- `napi_number_expected` if a non-number `napi_value` is passed in

### *napi_get_value_string_length*

#### Signature
```
napi_status napi_get_value_string_length(napi_env e, napi_value v, int* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: `napi_value` representing JavaScript string
- `[out] result`: Number of characters in the given JavaScript string

#### Return value
- `napi_ok` if the API succeeded.
- `napi_string_expected` if a non-string `napi_value` is passed in

### *napi_get_value_string_utf16*

#### Signature
```
napi_status napi_get_value_string_utf8(napi_env e,
                                       napi_value value,
                                       char16_t* buf,
                                       int bufsize,
                                       int* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  value`: `napi_value` representing JavaScript string
- `[in]  buf`: Buffer to write the UTF16-LE encoded string into
- `[in]  bufsize`: Size of the destination buffer. Use `napi_get_value_string_utf8_length` to 
get the required size of the buffer.
- `[out] result`: Number of bytes copied into the buffer including the null terminateor. 
If the buffer size is insufficient, the string will be truncated including a null terminator.

#### Return value
- `napi_ok` if the API succeeded.
- `napi_string_expected` if a non-string `napi_value` is passed in

### *napi_get_value_string_utf16_length*

#### Signature
```
napi_status napi_get_value_string_utf16_length(napi_env e, napi_value v, int* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: `napi_value` representing JavaScript string
- `[out] result`: Number of UTF16-LE 2-byte code units in the given JavaScript string

#### Return value
- `napi_ok` if the API succeeded.
- `napi_string_expected` if a non-string `napi_value` is passed in

### *napi_get_value_string_utf8*

#### Signature
```
napi_status napi_get_value_string_utf8(napi_env e,
                                       napi_value value,
                                       char* buf,
                                       int bufsize,
                                       int* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  value`: `napi_value` representing JavaScript string
- `[in]  buf`: Buffer to write the utf8 encoded string into
- `[in]  bufsize`: Size of the destination buffer. Use `napi_get_value_string_utf8_length` to 
get the required size of the buffer.
- `[out] result`: Number of bytes copied into the buffer including the null terminateor. 
If the buffer size is insufficient, the string will be truncated including a null terminator.

#### Return value
- `napi_ok` if the API succeeded.
- `napi_string_expected` if a non-string `napi_value` is passed in

### *napi_get_value_string_utf8_length*

#### Signature
```
napi_status napi_get_value_string_utf8_length(napi_env e, napi_value v, int* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: `napi_value` representing JavaScript string
- `[out] result`: Number of utf8 bytes in the given JavaScript string

#### Return value
- `napi_ok` if the API succeeded.
- `napi_string_expected` if a non-string `napi_value` is passed in

### *napi_get_value_uint32*

#### Signature
```
napi_status napi_get_value_uint32(napi_env e, napi_value v, uint32_t* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: `napi_value` representing JavaScript Number
- `[out] result`: C++ uint32 primitive equivalent of the given JavaScript Number

#### Return value
- `napi_ok` if the API succeeded.
- `napi_number_expected` if a non-number `napi_value` is passed in




