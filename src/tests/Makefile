# makefile for tolua executable

TOLUA=../..
TOLUAEXE = tolua

include $(TOLUA)/config

LIBS = -ltolua -l$(LUA) -lm 

EXE = tmodule tnamespace tclass tpeer tinheritance tconstant tvariable tfunction tarray tdirective tenum tvararg

all: $(EXE)
	./tmodule; ./tnamespace; ./tclass; ./tpeer; ./tinheritance; ./tconstant; ./tvariable; ./tfunction; ./tarray; ./tdirective; ./tfunction; ./tenum; ./tvararg

tarray: tarray.o tarraybind.o
	$(CC) $(CFLAGS) -o $@ tarray.o tarraybind.o $(TLIB) $(LIB) $(LIBS)
tarraybind.c: tarray.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tvariable: tvariable.o tvariablebind.o
	$(CC) $(CFLAGS) -o $@ tvariable.o tvariablebind.o $(TLIB) $(LIB) $(LIBS)
tvariablebind.c: tvariable.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tmodule: tmodule.o tmodulebind.o
	$(CC) $(CFLAGS) -o $@ tmodule.o tmodulebind.o $(TLIB) $(LIB) $(LIBS)
tmodulebind.c: tmodule.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tnamespace: tnamespace.o tnamespacebind.o
	$(CXX) $(CXXFLAGS) -o $@ tnamespace.o tnamespacebind.o $(TLIB) $(LIB) $(LIBS)
tnamespacebind.cpp: tnamespace.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tdirective: tdirectivebind.o
	$(CC) $(CFLAGS) -o $@ tdirectivebind.o $(TLIB) $(LIB) $(LIBS)
tdirectivebind.c: tdirective.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tfunction: tfunction.o tfunctionbind.o
	$(CXX) $(CXXFLAGS) -o $@ tfunction.o tfunctionbind.o $(TLIB) $(LIB) $(LIBS)
tfunctionbind.cpp: tfunction.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tconstant: tconstant.o tconstantbind.o
	$(CXX) $(CXXFLAGS) -o $@ tconstant.o tconstantbind.o $(TLIB) $(LIB) $(LIBS)
tconstantbind.cpp: tconstant.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tclass:  tclass.o tclassbind.o
	$(CXX) $(CXXFLAGS) -o $@ tclass.o tclassbind.o $(TLIB) $(LIB) $(LIBS)
tclassbind.cpp: tclass.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tpeer:  tpeer.o tpeerbind.o
	$(CXX) $(CXXFLAGS) -o $@ tpeer.o tpeerbind.o $(TLIB) $(LIB) $(LIBS)
tpeerbind.cpp: tpeer.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tinheritance:  tinheritance.o tinheritancebind.o
	$(CXX) $(CXXFLAGS) -o $@ tinheritance.o tinheritancebind.o $(TLIB) $(LIB) $(LIBS)
tinheritancebind.cpp: tinheritance.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tenum:  tenum.o tenumbind.o
	$(CC) $(CPPFLAGS) -o $@ tenum.o tenumbind.o $(TLIB) $(LIB) $(LIBS)
tenumbind.c: tenum.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

tvararg:  tvararg.o tvarargbind.o
	$(CXX) $(CXXFLAGS) -o $@ tvararg.o tvarargbind.o $(TLIB) $(LIB) $(LIBS)
tvarargbind.cpp: tvararg.pkg
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

.cpp.o:
	$(CXX) -c $(CXXFLAGS) -o $@ $<

.pkg.cpp:
	$(TOLUA)/bin/$(TOLUAEXE) -o $@ $<

clean:
	rm -f t*bind.* 
	rm -f *.o 

klean:
	rm -f $(EXE)



