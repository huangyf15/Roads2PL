#======================================
# Author: Yunfan Huang
# Institute: Tsinghua University
# Updated: Aug 07, 2020
#======================================
CODESPEED = DEBUG

PLATFORM = YUNFAN

COMPILER = GXX
LANGUAGE = CPP

WORKDIR = C:\\Users\\Yunfan\\Documents\\Codes\\Roads2PL\\C++\\courses
MAKEDIR = $(WORKDIR)\\THU01\\exp04_05
#==========================================================
# Directory aliases
TARGET = $(BINDIR)\\main
BINDIR = $(MAKEDIR)\\bin
OBJDIR = $(MAKEDIR)\\obj
SRCDIR = $(MAKEDIR)\\src
HEADDIR = $(SRCDIR)\\h
CPPDIR = $(SRCDIR)\\cpp
#==========================================================
# Platform and Compiler
ifeq ($(PLATFORM), YUNFAN)

MYLIB = $(HEADDIR)

ifeq ($(COMPILER), INTEL)
OPTLIB = 
INCLUDE =
LINK =
CC = icc
endif

ifeq ($(COMPILER), GXX)
OPTLIB =
INCLUDE = $(foreach LIB,$(MYLIB),-I $(LIB))
LINK =
ifeq ($(LANGUAGE), C)
CC = gcc
endif
ifeq ($(LANGUAGE), CPP)
CC = g++
endif
endif

endif
#==========================================================
# Compile Options
ifeq ($(COMPILER), INTEL)
IMODULE = -module
ACCF    =
FAST    = -O3
CFLAGS  = -O2
endif

ifeq ($(COMPILER), GXX)
IMODULE = -J
ACCF    =
FAST    = -O3
CFLAGS  = -O2
endif
#--------------------------------------
#- Debug options
ifeq ($(CODESPEED), DEBUG)
DEBUG   = -g
endif
ifeq ($(CODESPEED), FAST)
DEBUG   =
endif
#==========================================================
# Files' aliases
#- output src files' list
SRC = $(wildcard $(CPPDIR)/*.cpp)
SRCLIST = $(notdir $(SRC))
#- output *.cpp => *.o in the same dir
#- % means operating on each one
OBJLIST = $(patsubst %.cpp,%.o,$(SRCLIST))
#- output *.o => \\obj\\*.o
SRCOBJ = $(OBJLIST:%.o=$(OBJDIR)\\%.o)
#--------------------------------------
#- combine XXXOBJs into OBJ
OBJ = $(SRCOBJ) $(MODOBJ)
#- output present obj & bin files' list
OBJTMP = $(wildcard $(OBJDIR)/*.o)
TGTTMP = $(wildcard $(BINDIR)\\\\*.exe)
#- combine XXXOBJs into OBJ
OBJALL = $(OBJ) $(OBJTMP)
TGTALL = $(TARGET) $(TGTTMP)
#==========================================================
# Generate obj files
$(OBJDIR)\\%.o: $(CPPDIR)\\%.cpp
	$(CC) -c $^ $(CFLAGS) $(DEBUG) $(ACCF) $(INCLUDE) -o $@
#==========================================================
# Make options
.PHONY: all
all: $(TARGET)
$(TARGET): $(OBJ)
	$(CC) -o $(TARGET) $(OBJ) $(DEBUG) $(FAST) $(LINK)

.PHONY: cleanall cleanobj clean
cleanall: clean cleanobj

clean:
	rm -f $(TGTALL)

cleanobj:
	rm -f $(OBJALL)