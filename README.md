# CrossMap forked...

## What dis?

This is a fork of CrossMap (http://crossmap.sourceforge.net/) which has been modified so that VCF output can be bgzip compressed or piped on the fly. A couple of other bug fixes for VCF parsing have been added as well (see below).

## Why?

...because with huge VCF datasets it is sometimes necessary to output to compressed files or pipe to other commands. Neither of these options was available in the most recent version of CrossMap at the time of forking. This is based on v0.2.6 of CrossMap.

Furthermore, the mode to read from STDIN in CrossMap.py is broken. This fork fixes that issue.

Unfortunately there is no public git or svn repo for CrossMap, so these changes cannot be pushed upstream.

## How?

In addition to the python modules required by the standard version of CrossMap you will also need to have the Bio.bgzf module from biopython (biopython.org) if you want to write to bgzip compressed files. If you have pip installed you should be able to run the following to acheive this:

    pip install biopython

In order to **output to bgzip compressed files** simply **add '.gz' or '.bgz'** to the end of your output filename. Input can be in standard or compressed format. For example:

    bin/CrossMap.py vcf hg38ToHg19.over.chain.gz input.vcf.gz hg19.fa output.vcf.gz

When outputting compressed data, the '.unmap' file will also be compressed and have a matching '.gz' or '.bgz' extension.

In order to **pipe** your output (unix-like systems only), specify your output filename as /dev/stdout and pipe to your command of choice:

    bin/CrossMap.py vcf hg38ToHg19.over.chain.gz input.vcf hg19.fa /dev/stdout | <some_other_command>

When piping, unmapped data will be sent to a file named in the format 'crossmap_unmap_xxxxxx.vcf' where 'xxxxxx' will be random alphanumeric characters. 

This fork also fixes the ability to read from stdin. In the below example, if the input filename is given as '-' data will be read from STDIN.

    tabix -h input.vcf.gz chr1:1-20000000 | bin/CrossMap.py vcf hg38ToHg19.over.chain.gz - hg19.fa output.vcf.gz

## Additional fixes to VCF mapping

- The original code for CrossMap checks that the new REF field is not identical to the new ALT field, but this check is not sufficient to confirm that one ALT allele of a multiallelic variant is not identical to the REF allele. A fix is added to check each ALT allele of a multiallelic variant is not identical to the REF allele, with variants failing this check added to the unmapped file.

- The original code permits the liftover of variants where the overlapping region is partially deleted in the new reference. This leads to truncated REF alleles, which will not be a faithful representation of the what the genotyper was comparing with the ALT alleles. These variants are now filtered into the unmapped file.

## Credit

The code was modified by David A. Parry at University of Edinburgh to perform the operations described above. For details of the original software please see the website for the original version (http://crossmap.sourceforge.net/) and the citation:

    Zhao, H., Sun, Z., Wang, J., Huang, H., Kocher, J.-P., & Wang, L. (2013). CrossMap: a versatile tool for coordinate conversion between genome assemblies. Bioinformatics (Oxford, England), btt730.

## License

This program is free software: you can redistribute it and/or modify it under the terms of the GNU General Public License as published by the Free Software Foundation, either version 3 of the License, or (at your option) any later version. This program is distributed in the hope that it will be useful, but WITHOUT ANY WARRANTY; without even the implied warranty of MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE. See the GNU General Public License for more details. You should have received a copy of the GNU General Public License along with this program. If not, see <http://www.gnu.org/licenses/>.

