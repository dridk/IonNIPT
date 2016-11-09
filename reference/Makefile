

WISECONDOR_PATH=../wisecondor
RAW_PATH=raw
SRC= $(wildcard *.cram)
GCC = $(SRC:.cram=.gcc)
GC_COUNT=./ref/gccount
HG_19=hg19.fasta
IN_REF=./in/

all: reference
	 
%.bam: %.cram $(HG_19)
	samtools view -b $< -T $(HG_19) -@ 30 > $@

%.clean.bam : %.bam
	sambamba markdup -r  $^ $@ -t 30


%.pickle: %.clean.bam
	samtools view -q 1 $^|python $(WISECONDOR_PATH)/consam.py -outfile $@

%.gcc: %.pickle $(GC_COUNT)
	python $(WISECONDOR_PATH)/gcc.py $< $(GC_COUNT) $@


$(GC_COUNT): $(HG_19)
	mkdir -p ./ref 
	python $(WISECONDOR_PATH)/countgc.py $< $@

$(IN_REF): $(GCC)
	mkdir -p $@
	mv *.gcc $@

reference: $(IN_REF) 
	mkdir -p $(IN_REF)
	python $(WISECONDOR_PATH)/newref.py $< $@

.PHONY: clean 
clean:
	@rm -f *.pickle
	@rm -f *.gcc 
	@rm -Rf $(IN_REF)

