# uap-redshift

This is a fork of [] which uses [python-user-agents](https://github.com/selwin/python-user-agents) instead of [uap-python](https://github.com/ua-parser/uap-python).

## Installing
```sql
CREATE OR REPLACE LIBRARY uap_python LANGUAGE plpythonu
  FROM 'https://github.com/jcaip/uap-redshift/raw/master/uap-redshift.zip';
```

## Usage
```sql
-- set up a UDF for what you want extracted from the user_agent
CREATE OR REPLACE FUNCTION parse_ua_mobile(ua VARCHAR(1024))
RETURNS BOOL IMMUTABLE AS $$
  from user_agents import parse 
  return parse(ua).is_mobile
$$ LANGUAGE plpythonu;

```

## generic
```sql
-- UDF which is a bit more generic
CREATE OR REPLACE FUNCTION parse_ua(ua VARCHAR(1024), key VARCHAR)
RETURNS VARCHAR IMMUTABLE AS
$$
  from user_agents import parse
  ua_obj = parse(ua)

  def multi_getattr(obj, attr, default = None):
      """
      Get a named attribute from an object; multi_getattr(x, 'a.b.c.d') is
      equivalent to x.a.b.c.d. When a default argument is given, it is
      returned when any attribute in the chain doesn't exist; without
      it, an exception is raised when a missing attribute is encountered.

      """
      attributes = attr.split(".")
      for i in attributes:
          try:
              obj = getattr(obj, i)
          except AttributeError:
              if default:
                  return default
              else:
                  raise
      return obj
  return str(multi_getattr(ua_obj, key))

$$ LANGUAGE plpythonu;
```

select parse_ua(parsed_req_useragent, 'is_mobile') from logs.frontend limit 10;
