#===-- CBackend.cpp - Library for converting LLVM code to C --------------===//
#
#                     The LLVM Compiler Infrastructure
#
# This file is distributed under the University of Illinois Open Source
# License. See LICENSE.TXT for details.
#
#===----------------------------------------------------------------------===//
#
# Makefile for CBE test cases. Currently uses manual file identification.
#
#===----------------------------------------------------------------------===//

LLVMToolDir ?= ../../../build/bin

PYTHON = python
CC = gcc-4.8
CLANG = clang-3.9
CPPFLAGS = -Wall -Wno-unused-function -Wno-parentheses-equality -Wno-unused-variable -Wno-incompatible-library-redeclaration
FILES := $(patsubst %.c,%,$(filter-out %.cbe.c,$(wildcard *.c)))

all: cexec buildcbe

.PHONY: clean cexec buildcbe buildcbe* tollvm tollvm* all
.SECONDARY:

#Make each level of Optimization in one call
tollvm: tollvmO0 tollvmO1 tollvmO2 tollvmO3
tollvmO0: $(addsuffix _O0.ll,$(FILES))
tollvmO1: $(addsuffix _O1.ll,$(FILES))
tollvmO2: $(addsuffix _O2.ll,$(FILES))
tollvmO3: $(addsuffix _O3.ll,$(FILES))
%_O0.ll: %.c
	$(CLANG) $(CPPFLAGS) $< -emit-llvm -g -I./ -S -O0 -o $@
%_O1.ll: %.c
	$(CLANG) $(CPPFLAGS) $< -emit-llvm -g -I./ -S -O1 -o $@
%_O2.ll: %.c
	$(CLANG) $(CPPFLAGS) $< -emit-llvm -g -I./ -S -O2 -o $@
%_O3.ll: %.c
	$(CLANG) $(CPPFLAGS) $< -emit-llvm -g -I./ -S -O3 -o $@

tocbe: tocbeO0 tocbeO1 tocbeO2 tocbeO3
tocbeO%:  $(addsuffix _O%.cbe.c,$(FILES))

%.cbe.c: %.ll $(LLVMToolDir)/llvm-cbe
	$(LLVMToolDir)/llvm-cbe $<

buildcbe: buildcbeO0 buildcbeO1 buildcbeO2 buildcbeO3
buildcbeO0 : $(addsuffix _O0.cbe,$(FILES))
buildcbeO1 : $(addsuffix _O1.cbe,$(FILES))
buildcbeO2 : $(addsuffix _O2.cbe,$(FILES))
buildcbeO3 : $(addsuffix _O3.cbe,$(FILES))
%.cbe : %.cbe.c
	$(CC) $(CPPFLAGS) -I./ -o $@ -O$(shell echo $* | sed 's/^.*_O\([0-9]\)$$/\1/') $<

#Make C-Based Executables
cexec: $(addsuffix .cexe,$(FILES))
%.cexe: %.c
	$(CC) $(CPPFLAGS) -I./ -o $@ $<

test: all
	LLVMToolDir=$(LLVMToolDir) $(PYTHON) testFile.py

#Remove all .ll, .cbe.c, and .cbe files
clean:
	rm -rf *.ll
	rm -rf *.cbe
	rm -rf *.cexe
	rm -rf *.cbe.c
