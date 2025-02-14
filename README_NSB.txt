Hi devs of NSB, this is a future note from Alex

The original SimpleAmqpClient hasn't been updated in 5 years, and a few changes were necessary to fix some errors and warnings 
generated as a result.

1) Deprecated headers from rabbitmq-c

I was getting these warnings (the rest not included here):

In file included from /Users/alex/Desktop/Code/nsb/api/amqpclient/src/AmqpException.cpp:31:
/usr/local/include/amqp.h:10:5: warning: "amqp.h is deprecated, use rabbitmq-c/amqp.h instead." [-W#warnings]
#   warning "amqp.h is deprecated, use rabbitmq-c/amqp.h instead."
    ^
In file included from /Users/alex/Desktop/Code/nsb/api/amqpclient/src/AmqpException.cpp:32:
/usr/local/include/amqp_framing.h:11:5: warning: "amqp_framing.h is deprecated, use rabbitmq-c/framing.h instead." [-W#warnings]
#   warning "amqp_framing.h is deprecated, use rabbitmq-c/framing.h instead."
    ^

As the warning suggested, we had some outdated headers that needed to be changed.

2) Boost errors as a result of old CMakeFile.txt

I was getting these errors: 

In file included from /opt/homebrew/include/boost/lexical_cast.hpp:30:
/opt/homebrew/include/boost/lexical_cast/detail/buffer_view.hpp:27:34: error: expected '(' for function-style cast or type construction
        return buffer_view<CharT>{begin, end};
               ~~~~~~~~~~~~~~~~~~^
/opt/homebrew/include/boost/lexical_cast/detail/buffer_view.hpp:31:33: error: expected '(' for function-style cast or type construction
        return buffer_view<char>{
               ~~~~~~~~~~~~~~~~~^
/opt/homebrew/include/boost/lexical_cast/detail/buffer_view.hpp:38:33: error: expected '(' for function-style cast or type construction
        return buffer_view<char>{
               ~~~~~~~~~~~~~~~~~^

They were a result of the CMakeFile.txt in SimpleAmqpClient setting the C++ compiler version to 98, which is outdated for Boost, a prereq. 

The fix:

Find the below in CMakeFile.txt in SimpleAmqpClient:

```
if(NOT DEFINED CMAKE_CXX_STANDARD)
  set(CMAKE_CXX_STANDARD 98)
endif()
set(CMAKE_CXX_STANDARD_REQUIRED ON)
set(CMAKE_CXX_EXTENSIONS OFF)
```

and change `set(CMAKE_CXX_STANDARD 98)`
to `set(CMAKE_CXX_STANDARD 17)`

This will force the compiler to use C++17 instead, more compatible and up-to-date with boost



I've gone ahead and done the fixes above, so no action is needed on your part. This just serves as a note as to why this repo fork was necessary.