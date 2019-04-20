Rules
=====

Rule - Header guards should contain the fully-qualified case-sentive class name
-------------------------------------------------------------------------------

When writing guards in your header files, consider using the following format

.. code-block:: cpp
    :linenos:

    #ifndef __<namespace-names>_<case-sensive-filename>_h
    #define __<namespace-names>_<case-sensive-filename>_h

    // ... your stuff here

    #endif // __<namespace-names>_<case-sensive-filename>_h 

:Why:

* Historically, programmers tend to see but ignore preprocessor include guards
* Developers copy-paste a lot of code (this is good, it means your code is reusable)
* A search and replace usually occurs after copy-pasting a file
* An easy developer distraction can leads to two include guards being the same for different classes
* Making preprocessor guards search-and-replace friendly alleviates copy-paste errors

For Example (file src/acme/shared/OrderProcessing.h).  If a developer copy-pasted this class and did a search and replace on "OrderProcessing", the include guard will be included in the rename.

.. code-block:: cpp
    :linenos:

    #ifndef __acme_shared_OrderProcessing_h
    #define __acme_shared_OrderProcessing_h

    namespace acme {
    namespace shared {

    class OrderProcessing
    {
        // ... omitted for brevity
    }

    } // namespace shared
    } // namespace acme

    #endif // __acme_shared_OrderProcessing_h 


Rule - Namespace names should be lowercase and class names should be Pascal Case
--------------------------------------------------------------------------------

Namespace names should only contain lowercase characters and classes should be Pascal Case

:Why:

* Code needs to be readable
* Remember classes are usually contained inside namespaces
* Having different casing for (lowercase) namespaces and (mixed-case) classes makes code more readable
* Developers should be never question what is a class and what is a namespace when looking through code

For Example (file src/aws/s3/S3Object.h):

.. code-block:: cpp
    :linenos:

    namespace aws {
    namespace s3 {

    class S3Object
    {
    // ... omitted for brevity
    };

    } // namespace s3
    } // namespace aws


Rule - Namespace names should follow a very specific format
-----------------------------------------------------------

To be consistent with others, consider adopting a common namespacing format.  Each namespace endpoint (the point before a class exists) should be its own entity that contains a set of functions or classes that are exported and used in a module, library or product.

:Why:

* People inventing different formats make code look inconsistent
* Inconsistent code is hard to read and manage as it grows.
* Adopt a single format and be consistent.

Consider the specific namespace format:

.. code-block:: cpp

    <company>::<product>::<module>
    <company-product>::<service>::<component>

* Company is a global namespace for all code developed by that company, usually a domain name or a stock ticker.

    * Microsoft: microsoft, msft, ms
    * Amazon: amazon, amzn, amz, aws
    * National Instruments: natinst, ni
    * nVidia: nvidia, nv

* Product is typically something the company sells
* Service is typically a service the company uses/sells
* Modules is typically the set of libraries/binaries included in a product
* Component is typically sub-component inside a service or module
* Developers should strive for short but meaningful and readable names

Good Namespace Names:

.. code-block:: cpp

    ms::word::shared
    aws::dynamodb
    amz::smarthome

Bad Names:

.. code-block:: cpp
    :linenos:

    utils // too generic, no company name
    aws::public // too generic, no product called 'public'
    national_instruments::labview::api // too long and contains 'undercore'
    IBM::Watson::api // namespace should only contain lowercase


Rule - Enums should have a very specific format
-----------------------------------------------

Consider the specific enum format:

.. code-block:: cpp
    :linenos:

    typedef <identifier>Type <type>
    enum <identifier>Type

    <company><product><module>


Rule - Have 'if' statements check for error conditions and throw and never check positive (good) conditions
-----------------------------------------------------------------------------------------------------------

:Why:

* Makes code is more readable and more maintainable
* Throwing an exception on error conditions makes code more correct
* Code that checks for positive conditions usually miss and 'else' condition, which is the error condition, indicating a bug

Can you spot the error here?

.. code-block:: cpp
    :linenos:

    void SessionProxy::OpenSession(const std::string& userName)
    {
        if (!userName.empty()) {
            if (m_sessionMap.isIntialized()) {
                if (m_sessionMap.contains(userName)) {
                    m_sessions.OpenSession(userName);
                }
            }
        }
    }

Bugs

* A noop occurs when userName is empty
* A noop occurs when m_sessionMap is not initialized
* A noop occurs when userName is not inside sessions map

A better implementation

.. code-block:: cpp
    :linenos:

    void SessionProxy::OpenSession(const std::string& userName)
    {
        if (!m_sessionMap.isIntialized()) {
            throw Exception(kErrorInternalErrorSessionNotInitialized, LOCATION);
        }

        if (!m_sessionMap.contains(userName)) {
            throw Exception(kErrorInvalidUserName, LOCATION);
        }

        m_sessions.OpenSession(userName);
    }


Rule - Prefer const reference inputs have ampersand after type
--------------------------------------------------------------

Yes
std::string trim(const std::string& input);

No
std::string trim(const std::string &input); // ampersand before variable

:Why:

Consider what happens when we have long types, function names
std::string &getName(const std::string& input);

Rule 7 - Variable names matter
------------------------------

When assighing variable names, consider the follwoing scheme

* string types end with "Name": firstName, sessionName, displayName.
* vector or list types end with plural nouns, or "List": sessions, fileList
* paths end with he word "path": iniFilePath, configPath
* for-loop variables prefixed with the word "each".  for (const std::string& eachPath in pathList)
* boolean variables imply true/false relationship: isEnabled, enabled, 

Rule - Include a unit of measurement in variable/function names where applicable
--------------------------------------------------------------------------------

:Why:

* A common source of bugs is when developers use your API and make a simple mistake in interpreting a variable

Consider the following variable names (don't do this)

Bad variable names

.. code-block:: cpp

    int weight; // BAD - How heavy
    long timeout; // BAD - How long is timeout
    long startTime; // BAD - Start time since when?

Good variable names

.. code-block:: cpp

    int weightInLbs;
    long timeoutInMs;
    long startTimeSince1970;

Rule 8 - Beware of shortening variable names
--------------------------------------------

:Why:

* Short names can confuse other developers
* What is obvious to you is probably not obvious to others
* Good names are easy to debug and maintain.  Bad names are difficult to read and debug

.. code-block:: cpp
    :linenos:

    ms = GetTimeout(); // Not obvious what 'ms' is.  Ambiguity can lead to bugs.
    mSecs = GetTimeout(); // Does mSec mean milli seconds, micro seconds, or is it a member variale called Secs?
    pTree = GetPTree();  // That is PTree?  A pointer to a tree?  A property tree?  Something else?

A better implementation

.. code-block:: cpp
    :linenos:

    milliSeconds = GetTimeoutInMs();
    propertyTree = GetPropertyTree();

Rule - Preprocessor macros are not replacements for functions
-------------------------------------------------------------

Signs you are violating this rule

* You declare a variable in your macro
* You return a declared variable from a macro
* You have multiple if statements, complex loops, in your macro
* You have 3 or more lines of code in your macro

:Why:

* Multi-line macros that are complex are error-prone
* Multi-line macros bloat executable size because you don't take advantage of code reuse via inline functions
* Multi-line macros are difficult to debug and for tools to process.

If you have to write multi macros, be sure to

* isoloate each macro parameter
* isolate macro difinition inside a do/while(0) loop

Rule - Isolate multi-line macros inside a do/while loop
-------------------------------------------------------



Rule - Consider not using preprocssors
--------------------------------------

do while
brackets

Rule - 

Rule 9 - Consider making 0 value in enum an invalid/unknown/unset/null/none value
---------------------------------------------------------------------------------

For example

.. code-block:: cpp
    :linenos:

    enum FlightMode : int32 {
        kFlightModeNotSet = -1,
        kAutopilotOrbitalMode,
        kStraightMode,
        kSimpleLandingMode,
        kUserMode,
        kkMaxNumberFlightMode // always the last one
    }

:Why:

This bug is a bit nasty because it involves people using your enum in an unpected way.

What happens when someone declares the following

.. code-block:: cpp
    :linenos:

    FlightMode flightMode = 0; // I'm initializing like a good programmer

or in a class constructor

.. code-block:: cpp
    :linenos:

    Drone::Drone() : m_flightMode(0)
    {}

While the intention of the developer was to initiailize the variable to a known state, what is not obvious is that the variable was initialized to a *valid* state.

A better implementation

.. code-block:: cpp
    :linenos:

    enum FlightMode : int32{
        kFlightModeNotSet = 0,
        kAutopilotOrbitalMode,
        kStraightMode,
        kSimpleLandingMode,
        kUserMode,
        kkMaxNumberFlightMode // always the last one
    }

Rule 9 - Leftovers
------------------

input const reference
output (writable) by pointer
always check input validity before function starts
