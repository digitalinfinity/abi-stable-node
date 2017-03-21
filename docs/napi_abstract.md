# Abstract Operations (return napi objects or tell you the type)

NAPI exposes a set of APIs to apply some abstract operations on Javascript Objects.
Some of these operations are documented under [Section 7](https://tc39.github.io/ecma262/#sec-abstract-operations) 
of the [ECMAScript Language Specification](https://tc39.github.io/ecma262/).

These APIs allow you to do one of the following:
1. Coerce JavaScript objects to fundamental JavaScript types
2. Check the type of a JavaScript object
3. Check for equality between two Javascript Objects

## Functions

### *napi_coerce_to_bool*

#### Signature
```
napi_status napi_coerce_to_bool(napi_env e, napi_value v, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: The JavaScript object to coerce
- `[out] result`: `napi_value` representing the coerced JavaScript Boolean

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This NAPI API implements the abstract operation ToBoolean as defined in [Section 7.1.2](https://tc39.github.io/ecma262/#sec-toboolean)
of the ECMAScript Language Specification. 
This API can be re-entrant if getters are defined on the passed in Object.

### *napi_coerce_to_number*

#### Signature
```
napi_status napi_coerce_to_number(napi_env e, napi_value v, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: The JavaScript object to coerce
- `[out] result`: `napi_value` representing the coerced JavaScript Number

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This NAPI API implements the abstract operation ToNumber as defined in [Section 7.1.3](https://tc39.github.io/ecma262/#sec-tonumber)
of the ECMAScript Language Specification.
This API can be re-entrant if getters are defined on the passed in Object.

### *napi_coerce_to_object*

#### Signature
```
napi_status napi_coerce_to_object(napi_env e, napi_value v, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: The JavaScript object to coerce
- `[out] result`: `napi_value` representing the coerced JavaScript Object

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This NAPI API implements the abstract operation ToObject as defined in [Section 7.1.13](https://tc39.github.io/ecma262/#sec-toobject)
of the ECMAScript Language Specification.
This API can be re-entrant if getters are defined on the passed in Object.

### *napi_coerce_to_string*

#### Signature
```
napi_status napi_coerce_to_string(napi_env e, napi_value v, napi_value* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  v`: The JavaScript object to coerce
- `[out] result`: `napi_value` representing the coerced JavaScript String

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This NAPI API implements the abstract operation ToString as defined in [Section 7.1.13](https://tc39.github.io/ecma262/#sec-tostring)
of the ECMAScript Language Specification.
This API can be re-entrant if getters are defined on the passed in Object.

### *napi_get_type_of_value*

#### Signature
```
napi_status napi_get_type_of_value(napi_env e, napi_value vv, napi_valuetype* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  vv`: The JavaScript object whose type to query
- `[out] result`: The type of the JavaScript object

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This NAPI API represents behavior similar to invoking `typeof` Operator on the object 
as defined in [Section 12.5.5](https://tc39.github.io/ecma262/#sec-typeof-operator)
of the ECMAScript Language Specification.
However, it has support for detecting External objects, and also defaults to `napi_object`

### *napi_instanceof*

#### Signature
```
napi_status napi_instanceof(napi_env e, napi_value o, napi_value c, bool* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  o`: The JavaScript object to check
- `[in]  c`: The JavaScript object to check against
- `[out] result`: Whether `o instanceof c` is true.

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This NAPI API represents invoking `instanceof` Operator on the object 
as defined in [Section 12.10.4](https://tc39.github.io/ecma262/#sec-instanceofoperator)
of the ECMAScript Language Specification.

### *napi_is_array*

#### Signature
```
napi_status napi_is_array(napi_env e, napi_value value, bool* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  value`: The JavaScript object to check
- `[out] result`: Whether the given object is an array

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This NAPI API represents invoking `IsArray` operation on the object 
as defined in [Section 7.2.2](https://tc39.github.io/ecma262/#sec-isarray)
of the ECMAScript Language Specification.

### *napi_is_arraybuffer*

#### Signature
```
napi_status napi_is_arraybuffer(napi_env e, napi_value value, bool* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  value`: The JavaScript object to check
- `[out] result`: Whether the given object is an ArrayBuffer

#### Return value
- `napi_ok` if the API succeeded.

### *napi_is_buffer*

#### Signature
```
napi_status napi_is_buffer(napi_env e, napi_value value, bool* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  value`: The JavaScript object to check
- `[out] result`: Whether the given object is an napi representation of node:Buffer

#### Return value
- `napi_ok` if the API succeeded.

### *napi_is_error*

#### Signature
```
napi_status napi_is_error(napi_env e, napi_value value, bool* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  value`: The JavaScript object to check
- `[out] result`: Whether the given `napi_value` represents an Error object

#### Return value
- `napi_ok` if the API succeeded.

### *napi_is_typedarray*

#### Signature
```
napi_status napi_is_typedarray(napi_env e, napi_value value, bool* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  value`: The JavaScript object to check
- `[out] result`: Whether the given `napi_value` represents an TypedArray

#### Return value
- `napi_ok` if the API succeeded.

### *napi_strict_equals*

#### Signature
```
napi_status napi_strict_equals(napi_env e,
                               napi_value lhs,
                               napi_value rhs,
                               bool* result)
```

#### Parameters
- `[in]  e`: The environment that the API is invoked under
- `[in]  lhs`: The JavaScript object to check
- `[in]  rhs`: The JavaScript object to check against
- `[out] result`: Whether the two `napi_value` objects are equal

#### Return value
- `napi_ok` if the API succeeded.

#### Description
This NAPI API represents the invocation of the strict equality algorithm 
as defined in [Section 7.2.14](https://tc39.github.io/ecma262/#sec-strict-equality-comparison) 
of the ECMAScript Language Specification.