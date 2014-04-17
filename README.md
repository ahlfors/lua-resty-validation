# lua-resty-validation

**lua-resty-validation** is an extendable chaining validation and filtering library for Lua and OpenResty.

## Hello World with lua-resty-validation

```lua
local validation = require "resty.validation"

local valid, e = validation.type("number").between(0, 9)(5)  -- valid = true,  e = 5
local valid, e = validation.type("number").between(0, 9)(50) -- valid = false, e = "between"

-- Validators can also be reused
local smallnumber = validation.type("number").between(0, 9)
local valid, e = smallnumber(5)  -- valid = true,  e = 5
local valid, e = smallnumber(50) -- valid = false, e = "between"

-- Validators can do filtering (i.e. modify the value being validated)
-- valid = true, s = "HELLO WORLD!"
local valid, s = validation.type("string").upper()("hello world!")

-- You may extend the validation library with your own validators and filters...
validation.validators.reverse = function() 
  return function(value)
    if type(value) == "string" then
      return true, string.reverse(value)
    end
    return false
  end
end

-- ... and then use it
local valid, e = validation.reverse()("ABC") -- valid = true,  e = "CBA"
local valid, e = validation.reverse()(5)     -- valid = false, e = "reverse"

-- You can also group validate many values
local form = validation.new{ artist = "Eddie Vedder", number = 10 }
form.artist:validate(validation.type("string").len{ min = 5 })
form.number:validate(validation.equal(10))

if form.valid then
  print("all the form fields are valid")
else
  print(form.artist.name, form.artist.valid, form.artist.input,
        form.artist.value, form.artist.error, form.artist.invalid)
  print(form.number.name, form.number.valid, form.number.input,
        form.number.value, form.number.error, form.number.invalid)
end
```
