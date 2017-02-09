#MakeFile basics

Makefiles are great for compiling several files quickly and easily.

You can of course simply compile a file/set of files by doing
```bash
g++ main.cpp hello.cpp -o hello
```

An example makefile might look like:

```bash
CXXFLAGS=-std=c++11 $(shell root-config --cflags)
LIBS=$(shell root-config --libs)

run : hello_file
        @echo "Complied..."
hello_file : hello.o

        g++ -o $@ $^ ${LIBS}

%.o : %.cc
        g++ ${CXXFLAGS} -o $@ -c $^

```

To run your makefile you can simply type `make`. When you have your executable compiled, it can be run with `./hello`.
To recompile a file completely you can type instead `make -B`.
