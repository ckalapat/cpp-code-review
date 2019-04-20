Before Starting a New Project Rules
===================================

Before starting a new project, think of these things

1. What string encoding format should you use?
   * Native UTF8 encoding (preferred for multiplatform code)
   * Wide character format (preferred for Microsoft Windows-based code)
   * Mixing string encodings usually rots the code because tricks don't scale well between developers and teams
2. How should errors propogate through the system?
   * Exceptions (preffered for desktop and server-side applications)
   * Error codes (prefferred for embedded bare-metal projects)
   * Mixing error propogations usuully rots the code because code is diifcult to understand and debug

Before starting a new class, think of these things

1. How will it be constructed (default constructor)
2. How will it be copied (copy-constructor)
3. How will memory be used (operator=)
4. How will it be deleted (destructor)
5. What memory does it own (member variables) and how is that memory shared with others