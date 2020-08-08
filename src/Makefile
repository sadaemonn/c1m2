#******************************************************************************
# Copyright (C) 2017 by Alex Fosdick - University of Colorado
#
# Redistribution, modification or use of this software in source or binary
# forms is permitted as long as the files maintain this copyright. Users are 
# permitted to modify this and use it to learn about the field of embedded
# software. Alex Fosdick and the University of Colorado are not liable for any
# misuse of this material. 
#
#*****************************************************************************

#------------------------------------------------------------------------------
# Makefile can compile multiple source files for the msp432 and host platforms
#
# Use: make [TARGET] [PLATFORM-OVERRIDES]
#
# Build Targets:
#      IMPORTANT! : please specify PLATFORM when using each build target
#      <FILE>.i - Builds preprocessed output <FILE>.i
#      <FILE>.asm - Builds assembly output <FILE>.asm by objdump utility
#      <FILE>.o - Builds <FILE>.o object file
#      build - Builds and links all source files
#      compile-all - Compiles all objects but does not link
#      clean - Removes all generated files
# Platform Overrides:
#      PLATFORM - Platform the soruces will be compiled for  (HOST,MSP432) 
#
#------------------------------------------------------------------------------

# Platform Overrides
PLATFORM = HOST
include sources.mk
TARGET = c1m2
# Architectures Specific Flags
ifeq ($(PLATFORM),MSP432)
	LINKER_FILE = ../msp432p401r.lds
	CPU = cortex-m4
	ARCH = thumb
	SPECS =	nosys.specs
	CC = arm-none-eabi-gcc
	LD = arm-none-eabi-ld
	CFLAGS = -Wall -Werror -g -O0 -std=c99 -mcpu=$(CPU) -m$(ARCH) --specs=$(SPECS) -mfpu=fpv4-sp-d16 -mfloat-abi=hard -march=armv7e-m 
	LDFLAGS = -Wl,-Map=$(TARGET).map -T $(LINKER_FILE)
	CPPFLAGs = -DMSP432 
else ifeq ($(PLATFORM),HOST)
	CC = gcc
	LD = ld
	CFLAGS = -Wall -Werror -g -O0 -std=c99
	CPPFLAGs = -DHOST
	LDFLAGS = -Wl,-Map=$(TARGET).map
endif

# Compiler Flags and Defines

OBJS = $(SOURCES:.c=.o)
IS = $(SOURCES:.c=.i)
ASMS = $(SOURCES:.c=.asm)

%.i : %.c
	$(CC) -E $^ $(INCLUDES) $(CFLAGS) $(CPPFLAGs) -o $@

%.asm : %.o
	objdump -S $^ $^ > $@
	#I did not get why and how we should produce final executable

%.o : %.c
	$(CC) -c $^ $(INCLUDES) $(CFLAGS) $(CPPFLAGs) -o $@


.PHONY: build
build: $(TARGET).out

$(TARGET).out: $(OBJS)
	$(CC) $(OBJS) $(INCLUDES) $(CFLAGS) $(CPPFLAGs) $(LDFLAGS) -o $@

.PHONY: compile-all
compile-all: $(OBJS)

.PHONY: clean
clean:
	rm -f $(OBJS) $(IS) $(ASMS) $(TARGET).out $(TARGET).map

