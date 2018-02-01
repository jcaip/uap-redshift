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

