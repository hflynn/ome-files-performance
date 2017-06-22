[![DOI](https://zenodo.org/badge/78862024.svg)](https://zenodo.org/badge/latestdoi/78862024)

# OME Files Benchmark

The current repository contains the set of benchmark scripts used to
test the performance of the C++-based OME Files library.

## Benchmark datasets

Three public reference OME-TIFF datasets were used for performance
tests. For each dataset, we computed the metadata size-- the size in
bytes of the raw OME-XML string stored in the ImageDescription TIFF
tag-- and the pixeldata size-- the size in bytes of the binary pixel
data stored as TIFF. The test datasets are:

- “5D”, a multi-dimensional fluorescence image with 10 Z-sections, 2
  channels, 43 timepoints available at
  https://downloads.openmicroscopy.org/images/OME-TIFF/2016-06/tubhiswt-4D/.
  The metadata size is 176KiB and the size of the pixeldata is 216MiB.
- “Plate”, a plate containing 384 wells and 6 fields, derived from the
  [Broad Bioimage Benchmark
  Collection](https://data.broadinstitute.org/bbbc/) resource
  described in [Ljosa V, Sokolnicki KL, Carpenter AE (2012). Annotated
  high-throughput microscopy image sets for validation. Nature Methods
  9(7):637](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3627348/) and
  available at
  https://downloads.openmicroscopy.org/images/OME-TIFF/2016-06/BBBC/. The
  metadata size is 2.3MiB and the size of the pixeldata is 3.4GiB.
- “ROI”, a time-lapse sequence with ~13K regions of interest, derived
  from the [MitoCheck project](http://www.mitocheck.org/) described in
  [Neumann B et al. (2010). Phenotypic profiling of the human genome
  by time-lapse microscopy reveals cell division genes. Nature
  464(7289):721](https://www.ncbi.nlm.nih.gov/pmc/articles/PMC3108885/)
  and available at
  https://downloads.openmicroscopy.org/images/OME-TIFF/2016-06/MitoCheck/.
  The metadata size is 3.2MiB and the size of the pixeldata is 130MiB.

The datasets were chosen to test different aspects of library
performance. The Plate and ROI datasets are both single OME-TIFF
derived from real-world examples where the file content is either
dominated by the pixeldata or the metadata. 5D represents file layouts
where the pixeldata is distributed over multiple files. For more
information, see
https://www.openmicroscopy.org/site/support/ome-model/ome-tiff/data.html.

## Benchmark hardware and software

The benchmark scripts have been executed using two identical sleds in
a single server provisioned with the same hardware (Dell PowerEdge™
C6220, 2x E5-2640/96 GB). Windows 2008 Server was installed on one
sled, Ubuntu 16.04 running in a Docker virtualized environment was
installed on the other.

Our benchmark tests measured the performance of the current versions
of our two reference libraries: [OME Files C++
0.3.1](http://downloads.openmicroscopy.org/ome-files-cpp/0.3.1/) and
[Bio-Formats version
5.3.4](http://downloads.openmicroscopy.org/bio-formats/5.3.4/).  The
Bio-Formats library was executed directly using the Java Virtual
Machine but also over C++ using the Bio-Formats [JNI/JACE C++
bindings](https://github.com/ome/bio-formats-jace).  All benchmark
tests have been executed under both Windows and Linux environments
with the exception of the JNI/JACE C++ bindings which were only
successfully built under Ubuntu 16.04. We used Java Development Kit
(`1.7.0_80` on Windows and `1.7.0_95` on Linux) to build and run the
benchmark scripts as JDK7 is currently required to build the
Bio-Formats JACE C++ bindings.

## Benchmark tests

For each of the datasets above, four benchmark tests were executed:

- metadata.read: the metadata is extracted from the OME-TIFF
  ImageDescription tag and converted into OME Data Model objects using
  the ``createOMEXMLMetadata`` API
  ([Java](https://downloads.openmicroscopy.org/bio-formats/5.3.4/api/loci/formats/services/OMEXMLService.html#createOMEXMLMetadata-java.lang.String-)
  /
  [C++](https://downloads.openmicroscopy.org/ome-files-cpp/0.3.1/21/docs/ome-files-bundle-docs-0.3.1-b21/ome-files/api/html/namespaceome_1_1files.html#a469d4ec5c1bddd7b3afc0daa11ba1989))
- metadata.write: the metadata is serialized using the ``getOMEXML``
  API
  ([Java](https://downloads.openmicroscopy.org/bio-formats/5.3.4/api/loci/formats/services/OMEXMLService.html#getOMEXML-loci.formats.meta.MetadataRetrieve-)
  /
  [C++](https://downloads.openmicroscopy.org/ome-files-cpp/0.3.1/21/docs/ome-files-bundle-docs-0.3.1-b21/ome-files/api/html/namespaceome_1_1files.html#ad2898e87098e67fdda2154d7883692e0))
  and written to disk as an OME-XML file
- pixeldata.read: the pixeldata is read from the OME-TIFF using the
  ``openBytes`` API
  ([Java](https://downloads.openmicroscopy.org/bio-formats/5.3.4/api/loci/formats/IFormatReader.html#openBytes-int-byte:A-)
  /
  [C++](https://downloads.openmicroscopy.org/ome-files-cpp/0.3.1/21/docs/ome-files-bundle-docs-0.3.1-b21/ome-files/api/html/classome_1_1files_1_1detail_1_1FormatReader.html#a2106d1dd7b4f4fe6597fde5cdbdb0f37))
  and stored in memory
- pixeldata.write: the pixeldata is written to disk as another
  OME-TIFF using the ``saveBytes`` API
  ([Java](https://downloads.openmicroscopy.org/bio-formats/5.3.4/api/loci/formats/IFormatWriter.html#saveBytes-int-byte:A-)
  /
  [C++](https://downloads.openmicroscopy.org/ome-files-cpp/0.3.1/21/docs/ome-files-bundle-docs-0.3.1-b21/ome-files/api/html/classome_1_1files_1_1detail_1_1FormatWriter.html#a51115641c238f5830f796c1839d75872))

Each benchmark test records the real time in milliseconds before and
after each test, and computes the elapsed time from the difference.

## Building and executing the benchmark scripts

See the
[OME Files C++](https://www.openmicroscopy.org/site/support/ome-files-cpp/ome-cmake-superbuild/manual/html/building.html) and
[Bio-Formats](https://www.openmicroscopy.org/site/support/bio-formats/developers/building-bioformats.html) building instructions.

### Windows

The Windows build requirements are [Cmake](https://cmake.org/),
[Maven](http://maven.apache.org/), [Visual
Studio](https://www.visualstudio.com/) and a local version of the
standalone OME Files bundle matching the Visual Studio version.

For running our builds, we used the Continuous Integration software
[Jenkins](https://jenkins.io/index.html) to trigger the Windows
benchmark builds. A single script executing the building and execution
steps is available under
[jenkins_build.bat](scripts/jenkins_build.bat).

To build the OME Files performance scripts manually, within a `build`
directory, execute the following `cmake` command:

    $ cmake -G "Ninja" -DCMAKE_VERBOSE_MAKEFILE:BOOL=%verbose%
      -DCMAKE_INSTALL_PREFIX:PATH=%installdir% -DCMAKE_BUILD_TYPE=%build_type%
      -DCMAKE_PREFIX_PATH=%OME_FILES_BUNDLE%
      -DCMAKE_PROGRAM_PATH=%OME_FILES_BUNDLE%\bin
      -DCMAKE_LIBRARY_PATH=%OME_FILES_BUNDLE%\lib 
      -DBOOST_ROOT=%OME_FILES_BUNDLE% %sourcedir% 
    $ cmake --build .
    $ cmake --build . --target install

The Bio-Formats performance script can be built within the source
directory using Maven:

    $ cd source
    $ call mvn clean install

### Linux

The Linux benchmark was performed on Ubuntu 16.04. To ease the
distribution and reproducibility of the suite, the benchmark
environment is built using [Docker](https://www.docker.com/) via a
[Dockerfile](Dockerfile) including the software prerequisites and the
benchmark code.

To retrieve the
[Docker image](https://hub.docker.com/r/openmicroscopy/ome-files-performance),
run::

    $ docker pull openmicroscopy/ome-files-performance:latest

For testing, you can pull the
[Docker image](https://hub.docker.com/r/snoopycrimecop/ome-files-performance)
built daily including all the Pull Requests:

    $ docker pull snoopycrimecop/ome-files-performance:master_merge_trigger

To build the Docker image locally, run:

    $ docker build -t ome-files-performance .

Create a unique benchmark directory which will be mounted as the `/data`
volume when running the Docker container, e.g.:

    $ mkdir /temporary/benchmark-DD-MM-YYYY

where `DD-MM-YYYY` would be the date of execution of the benchmark.

This structure of this benchmark directory will be the following:

-   if the benchmark expects specific input datasets as specified in the
    ome-files-performance README.md, copy this data under the root folder of
    the benchmark directory e.g.
    `/temporary/benchmark-DD-MM-YYYY/tubhiswt-4D/`,
-   the results generated by the benchmark will be stored under a subfolder
    called `results`,
-   the temporary files generated by the benchmark will be stored under a
    subfolder called :file:`out`.

The default `docker run` command will execute the benchmarks defined by the `scripts/run_benchmarking` script without argument:

    $ docker run --rm -it -v /temporary/benchmark-DD-MM-YYYY:/data openmicroscopy/ome-files-performance:latest

To execute a particular benchmark, start a shell in the Docker container using
`docker run --entrypoint`, e.g.:

    $ docker run --rm -it --entrypoint=bash -v /temporary/benchmark-DD-MM-YYYY:/data snoopycrimecop/ome-files-performance:master_merge_trigger

Then from the shell, execute the desired benchmark::

    root@084bb88d5a62:/git/ome-files-performance$ ./scripts/run_benchmarking metadata

## Benchmark results

The [results](results) folder contains the final set of results
generated using the benchmark tests described above with the following
columns:

- `test.lang`: name of the benchmark environment (Java, C++, Jace)
- `test.name`: name of the benchmark test
- `test.file`: name of the benchmark dataset
- `proc.real`/`real`: execution time measured by the benchmark script

From these tab-separated value files, the following metrics have been defined
for the assessment of each benchmark test:

- performance is defined as the inverse of the execution time for each
  benchmark test
- relative performance of a test is defined as the ratio of the
  performance over the performance of the same test for the same
  dataset executed using Bio-Formats under Linux or Windows, as
  appropriate,
- metadata rate i.e. the rate of XML transfer per unit of time
  expressed in MiB/s is defined as the ratio of the metadata size of
  the test dataset over the execution time of the metadata test,
- pixeldata rate i.e. the rate of binary pixeldata transfer per unit
  of time expressed in MiB/s is defined as the ratio of the the
  pixeldata size of the test dataset over the execution time of the
  pixeldata test.

The benchmark metrics have been derived from twenty independent
iterations of each benchmark test. The only exception is the Plate
dataset pixeldata performance test which has only been reproduced six
times as a result of its long execution time (~1.5hr per test).

The [analysis](analysis) folder contains the plots and summarized data
after processing the data with [GNU R](https://www.r-project.org/) and
the script [plots.R](scripts/plots.R).

[Figure 2](analysis/files-fig2.pdf) is a copy of the figure submitted
for publication. The read and write performance of pixeldata and
metadata are compared for three software implementations and three
datasets. The relative performance of Linux implementations are
normalized based upon the Java/Linux results, while the Windows
implementations are normalized based upon the Java/Windows results.
The read and write performance of C++ is faster than Java in all
cases. However, while the JNI read performance is equivalent to Java,
the JNI write performance is much slower, indicating performance
problems which scaled with dataset size, approaching two orders of
magnitude slower with the largest dataset. C++ metadata read
performance was faster than Java for datasets with minimal metadata,
but slower with datasets containing large amounts of metadata. C++
metadata write performance was consistently faster than Java. JNI
metadata performance was equivalent or very similar to the Java
performance. [Supplementary Figure 1](analysis/files-suppfig1.pdf)
presents the same data with the Implementation and Test categories
swapped, to allow for easier comparison of implementation differences.
[Supplementary Figure 2](analysis/files-suppfig2.pdf) shows the
absolute timings for the above two figures, including the Java/Linux
and Java/Windows results previously omitted due to normalisation.

Summary data is presented in these tables:

- [pixeldata](analysis/summary-pixeldata-separate.tsv)
- [metadata](analysis/summary-metadata-separate.tsv)

Additionally, we have reproduced the benchmark by repeating the same
number of iterations of each test in a loop within the same
environment. We include these results for completeness and to indicate
the performance achieved if the same operation is invoked within a
process multiple times. [Supplementary Figure
3](analysis/files-suppfig3.pdf) and [Supplementary Figure
4](analysis/files-suppfig4.pdf) show the test results with repeated
iterations within the same environment. These may be compared with
Supplementary Figures 1 and 2. The pixeldata results are almost
identical with no significant changes. The metadata results however
show an improvement in Java performance, with C++ metadata reading
being slower than Java in all cases, while C++ retained a slight edge
over Java when writing. JNI performance was faster than Java for the
simplest dataset, and marginally faster for datasets with more complex
metadata.

[Supplementary Figure 5](analysis/files-suppfig5.pdf) and
[Supplementary Figure 6](analysis/files-suppfig6.pdf) present the same
data in more detail as boxplots, showing the distribution of timings
within each treatment. Figure 6 shows the effects of the Java
[HotSpot](https://en.wikipedia.org/wiki/HotSpot) optimizer when the
same routines are invoked repeatedly. The outliers for metadata in
particular highlight the significant reduction in runtime over each
iteration before approaching a plateau.

Summary data is presented in these tables:

- [pixeldata](analysis/summary-pixeldata-repeated.tsv)
- [metadata](analysis/summary-metadata-repeated.tsv)

## Benchmark conclusions

- For pixeldata tests, the results were found to be identical and
  independent of whether the test replicates were run separately or
  repeatedly. Interestingly, in the case of the metadata tests using
  Bio-Formats and the Java Virtual Machine (JVM), there is a notable
  performance gain due to the optimizer in the JVM. This gain is small
  for pixeldata, but large for metadata.

- C++ pixeldata reading and writing is significantly faster than Java
  in all cases, indicating that C++ should be preferred for workloads
  requiring the fastest possible data transfer rates.

- C++ metadata reading showed poor scalability compared with Java.
  Further profiling showed this was largely time spent in the
  Xerces-C++ XML library, which appears to have some inefficiencies in
  its implementation compared with the Xerces-Java library.

- C++ metadata writing was faster than Java, though Java approached
  the C++ performance when the HotSpot optimizer was allowed to
  effectively optimize the Java bytecode.

- JNI performance is broadly equivalent to Java, which is to be
  expected since in both cases the same code is running inside the
  JVM. However, the performance of pixeldata writing is much slower
  than expected, indicating a severe performance bottleneck in the
  JNI/JACE wrapper code. JNI/JACE provides no clear performance
  advantage over using Java directly.

## References

- [OME Files documentation](https://www.openmicroscopy.org/site/support/ome-files-cpp/)
- [Bio-Formats documentation](https://www.openmicroscopy.org/site/support/bio-formats)
