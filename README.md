# looper

This module was created after getting sick of the limited functionality of burp intruder. This allows for the iteration of complex datasets in order to allow for wide coverage.

* looper.iterutil
  * Itertools extension for generating large and complex datasets using generators.

* looper.BaseTestCase
  * The base test case

* looper.SimpleHTTP
  * A set of mixins which use urllib2

* looper.blackmambaHTTP
  * A set of test case mixins which use rootfoo's blackmamba

### Simple URL Iteration
```
#!/usr/bin/env python
import sys,datetime,random
from pprint import pprint
sys.path.append('libs')
from looper import iterutil,SimpleHTTP
    
params = iterutil.chain(
    iterutil.dict_zip(
        method = iterutil.repeat('GET'),
        url = iterutil.concat(
            iterutil.repeat('http://'),
            iterutil.repeat('example.com'),
            ["/uri","/uri1"],
        ),
        headers = iterutil.dict_zip({
            'User-Agent': iterutil.repeat('SecurityInnovation/0.0.1/Looper'),
            'Accept': iterutil.repeat("application/json"),
            'Date': iterutil.repeat_f(lambda: datetime.datetime.utcnow().strftime("%a, %d %b %Y %H:%M:%S GMT")),
            'X-Request-Id': iterutil.repeat_f(lambda:"%032x" % random.getrandbits(128)),
            'Content-Type': iterutil.repeat('application/json'),
        }),
    ),
)

test = SimpleHTTP.TestCase(params)
test.run()
```

### JSON POST example
```
#!/usr/bin/env python
import sys,datetime,random,json
from pprint import pprint
sys.path.append('libs')
from looper import iterutil,SimpleHTTP
    
params = iterutil.chain(
    iterutil.dict_zip(
        method = iterutil.repeat('POST'),
        url = iterutil.repeat('http://seattlenetworks.com/api'),
        headers = iterutil.dict_zip({
            'User-Agent': iterutil.repeat('SecurityInnovation/0.0.1/Looper'),
            'Accept': iterutil.repeat("application/json"),
            'Date': iterutil.repeat_f(lambda: datetime.datetime.utcnow().strftime("%a, %d %b %Y %H:%M:%S GMT")),
            'Content-Type': iterutil.repeat('application/json'),
        }),
        body = iterutil.imap(
            json.dumps,
            iterutil.dict_zip({
                'int': range(10),
                'rand_str': iterutil.repeat_f(lambda:"%032x" % random.getrandbits(128)),
            })
        )
    ),
)

test = SimpleHTTP.TestCase(params)
test.run()
```

For more examples and stub test cases, see the tests directory.
