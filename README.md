Element: Service
================

###### Element: Service

Everything is a decoration to the *Service*
-------------------------------------------

If there was no concern about HTML formatting, authentication just a pure program (function or operation) to run, it would be the service. A publicly accessible resources pass the request to the Service. The results vary (*plain-text, JSON, HTML, XML,…*), but all perform same operation.

**Service should be strictly decoupled from representation.**

Service is just a interface, the implementation is up to YOU. It is your application, your next Anygram or Anybook.

See the example of statuses. All public methods (API) maps to [HTTP/1.1 request verbs](http://en.wikipedia.org/wiki/Hypertext_Transfer_Protocol#Request_methods):

- `GET` - retrieves a resource
- `POST` - creates a resource
- `PUT`/`PATCH` - updates a resource
- `DELETE` - deletes a resource
- …

and the 2<sup>nd</sup> URI part or fall back to `INDEX`.

Examples:

```
GET    /statuses             > GETIndex()        #1
GET    /statuses/123         > GETIndex(123)     #2
GET    /statuses/latest      > GETLatest()       #3
GET    /statuses/123/tags    > GETItags(123)     #4
POST   /statuses             > POSTIndex()       #5
PUT    /statuses/123         > PUTIndex()        #6
DELETE /statuses/123         > GETIndex(123)     #2
```

\#5 and \#6 are little bit conflicting, by now only \#5 can be invoked (3<sup>rd</sup> parameter can be tested).

`Auth` prefix means a request must suffice some authorisation.

> **Heads up:** Use the [Handler](https://github.com/attitude/elements-handler) to handle [Request](https://github.com/attitude/elements-request) automatically and return.

```php
<?php

namespace fakebook;

use \attitude\Elements\Service_Interface;

use \attitude\Elements\Singleton_Prototype;

use \attitude\Elements\Request_Element;
use \attitude\Elements\HTTP_Exception;
use \attitude\Elements\Dependency_Container;

class Status_Service extends Singleton_Prototype implements Service_Interface
{
    private $storage = null;

    protected function __construct()
    {
        $this->storage = new Status_Storage();
    }

    public function GETIndex(Request_Element $request)
    {
        if ($id = $request->getUUID()) {
            return $this->storage->get($id);
        }

        return $this->storage->find();
    }

    public function AuthDELETEIndex(Request_Element $request)
    {
        if ($id = $request->getUUID()) {
            return $this->storage->delete($id);
        }

        return false;
    }

    public function AuthPUTIndex(Request_Element $request)
    {
        if ($id = $request->getUUID()) {
            return $this->storage->replace($id, $request->getRequestBody());
        }

        return false;
    }

    public function AuthPOSTIndex(Request_Element $request)
    {
        try {
            $result = $this->storage->store($request->getRequestBody());
        } catch (HTTP_Exception $e) {
            throw $e;
        }

        return $result;
    }
}
```

**Enjoy!**

[@martin_adamko](http://twitter.com/martin_adamko)  
*Say hi on Twitter*
