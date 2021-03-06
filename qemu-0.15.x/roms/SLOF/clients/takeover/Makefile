# *****************************************************************************
# * Copyright (c) 2004, 2008 IBM Corporation
# * All rights reserved.
# * This program and the accompanying materials
# * are made available under the terms of the BSD License
# * which accompanies this distribution, and is available at
# * http://www.opensource.org/licenses/bsd-license.php
# *
# * Contributors:
# *     IBM Corporation - initial implementation
# ****************************************************************************/

include $(TOPCMNDIR)/make.rules

SNKDIR = $(TOPCMNDIR)/clients/net-snk

CFLAGS += -fno-builtin -I$(LIBCMNDIR)/libc/include
CFLAGS += -I$(SNKDIR)/include -I. $(CPUARCHDEF)
CFLAGS += -I$(INCLBRDDIR) -I.. -I$(INCLCMNDIR)/$(CPUARCH)
CFLAGS += -O2 -msoft-float -Wa,-mregnames $(RELEASE)

OBJS = $(SNKDIR)/kernel/kernel.o
OBJS += $(SNKDIR)/oflib/oflib.o
OBJS += $(SNKDIR)/libc/time/timer.o
OBJS += $(LIBCMNDIR)/libc.a entry.o main.o of.elf takeover.o

%.o: %.S
	$(CC) $(CFLAGS) -c $^

all: takeover.elf 

takeover.elf: ppc32wrap.o takeover.elf32
	@echo " ====== Building $@ ======"
	$(LD) -N -melf32ppclinux -static -nostdlib \
		-Ttext=0x400000 -Tdata=0x400100 \
		$(LDFLAGS) $^ -o $@

takeover.elf64: entry.o main.o takeover.o $(SNKDIR)/libc/time/timer.o of.elf
	make -C $(LIBCMNDIR) libc
	make -C $(CLIENTSDIR)
	$(LD) $(LDFLAGS) -o $@ -Tclient.lds $(OBJS)

of.elf: ../../boot_rom.bin
	$(OBJCOPY) --input-target=binary --binary-architecture=powerpc -O elf64-powerpc $< $@

takeover.elf32: takeover.elf64
	$(OBJCOPY) -O binary $^ takeover.tmp 
	$(OBJCOPY) --input-target=binary --binary-architecture=powerpc -O elf32-powerpc takeover.tmp $@

ppc32wrap.o: ppc32wrap.S
	$(CROSS)gcc -m32 -a32 $(CFLAGS) -c $< -o $@

clean distclean:
	make -C $(LIBCMNDIR) $@
	make -C $(CLIENTSDIR) $@
	$(RM) *.o *.bin *.elf
	$(RM) takeover.elf32 takeover.elf64 takeover.tmp
%.o: %.oco
	cp -f $< $@
