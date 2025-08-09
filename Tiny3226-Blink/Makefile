#
# Basic Makefile for a small project.
# Just an example to show all required steps.
#

Project     = Blink
DeviceType  = attiny3226
SourceFiles = Main.cpp

# Compiler command-line options
CppOptions  = -mmcu=$(DeviceType)  # Specify target device type
CppOptions += -Wall                # All warnings enabled
CppOptions += -g                   # Include Debug info (Needed for source-code in .lst)
CppOptions += -O2                  # Optimise level 2

# Specify device package (as this is not included in my old copy of the compiler)
CppOptions += -I ../../Library/Packages/include
CppOptions += -B ../../Library/Packages/gcc/dev/attiny3226

# Do all steps
All : Objects/$(Project).elf Objects/$(Project).hex Objects/Eeprom.hex Objects/$(Project).lst Size Update

# Generate .elf file from sources
Objects/$(Project).elf : $(SourceFiles)
	mkdir -p Objects
	avr-gcc $(CppOptions) $(SourceFiles)  -o Objects/$(Project).elf

# Extract firmware .hex file from .elf
Objects/$(Project).hex  : Objects/$(Project).elf
	avr-objcopy -O ihex -R .eeprom   Objects/$(Project).elf   Objects/$(Project).hex

# Extract .hex file with eeprom data
EepromOptions  = -j .eeprom
EepromOptions += --set-section-flags=.eeprom=alloc,load
EepromOptions += --no-change-warnings
EepromOptions += --change-section-lma .eeprom=0
Objects/Eeprom.hex : Objects/$(Project).elf
	avr-objcopy -O ihex $(EepromOptions) Objects/$(Project).elf Objects/Eeprom.hex

# Extract listing for examining generated code
Objects/$(Project).lst : Objects/$(Project).elf
	avr-objdump -h --demangle -x  -S Objects/$(Project).elf > Objects/$(Project).lst

# Print memory-size of generated firmware
Size :
	avr-size Objects/$(Project).elf

# =================================================================================================
# Write firmware into target processor
UpdiPort = /dev/ttyUSB0
UpdiBaud = 230400

AvrDude  = avrdude              # avrdude command
AvrDude += -vv                  # Verbose. Print progress and info. More v's is more data
AvrDude += -c serialupdi        # Use 'serialupdi' as programmer type
AvrDude += -p $(DeviceType)     # Specify target device type
AvrDude += -P $(UpdiPort)       # Comport for UPDI to target
AvrDude += -B $(UpdiBaud)       # Baudrate for UPDI to target

# Write firmware (from Objects/Blink.hex)
Update :
	$(AvrDude) -U flash:w:Objects/$(Project).hex:i

# Read fuses bits into Hex file 'Fuses.hex'
ReadFuses :
	$(AvrDude) -U fuses:r:Fuses.hex:i

# Write fuses bits
#  - Add fuse-bit instructions as needed for your project
FuseBits  = -U wdtcfg:w:0x00:m  # Default value
FuseBits += -U bodcfg:w:0x00:m  # Default value

WriteFuses :
	$(AvrDude) $(FuseBits)

# Delete all generated files and subdirectories
# - Works on Linux, Windows may need another command.
Clean :
	rm -rf Objects

