ARDUINO_CORE := ../../../arduino/core.a

PART := atmega328
COMPILE_PART := $(PART)
ifeq ($(COMPILE_PART), atmega328)
	COMPILE_PART=atmega328p
endif

# Only programmed fuses are SPIEN and BODLEVEL1 (no BOOTRST)
LFUSE=0xff
ifneq (,$(findstring 88,$(PART)))
HFUSE=0xdd
EFUSE=0x07
else ifneq (,$(findstring 328,$(PART)))
HFUSE=0xdf
EFUSE=0x05
else
$(error Unconfigured part $(PART) (please add to makefile))
endif

%.o: %.cpp Makefile
	/opt/arduino-1.0.5/hardware/tools/avr/bin/avr-g++ -c -g -Os -w -fno-exceptions -ffunction-sections -fdata-sections -mmcu=$(COMPILE_PART) -DF_CPU=16000000L -MMD -DUSB_VID=null -DUSB_PID=null -DARDUINO=105 -I/opt/arduino-1.0.5/hardware/arduino/cores/arduino -I/opt/arduino-1.0.5/hardware/arduino/variants/standard $< -o $@

%.elf: %.o $(ARDUINO_CORE) Makefile
	/opt/arduino-1.0.5/hardware/tools/avr/bin/avr-gcc -Os -Wl,--gc-sections  -mmcu=$(COMPILE_PART)  -o  $@  $<  $(ARDUINO_CORE) -lm

%.hex: %.elf Makefile
	/opt/arduino-1.0.5/hardware/tools/avr/bin/avr-objcopy -O ihex -R .eeprom $< $@

.PHONY: prog_% prog
prog_%: %.hex
	/opt/arduino-1.0.5/hardware/tools/avrdude -b57600 -C/opt/arduino-1.0.5/hardware/tools/avrdude.conf -p$(PART) -cstk500v1 -P/dev/ttyUSB0 -e -Ulock:w:0x3F:m -Uefuse:w:$(EFUSE):m -Uhfuse:w:$(HFUSE):m -Ulfuse:w:$(LFUSE):m -Uflash:w:$<:i -v -v

prog: prog_controller

.PHONY: self_prog
self_prog_%: %.hex
	/opt/arduino-1.0.5/hardware/tools/avrdude -b115200 -C/opt/arduino-1.0.5/hardware/tools/avrdude.conf -patmega328p -carduino -P/dev/ttyUSB0 -Uflash:w:$<:i -v -v

.PHONY: clean
clean:
	rm -fv *.o *.elf *.hex

