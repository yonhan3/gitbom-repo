# gitbom-repo
A repository of gitBOM docs for Linux binaries

All the gitBOM docs are created using the [bomsh](https://github.com/git-bom/bomsh) tool.

The build-reproducible packages like hostname/sysstat are re-built with the same build environment,
in order to recreate the byte-to-byte idential binary files. The bomtrace2 program from the bomsh tool
is used to invoke hookup script bomsh_hook2.py, to create the gitBOM docs.

The bomsh tool also creates some metadata documents. The bomsh_gitbom_doc_mapping file is one of those.
This bomsh_gitbom_doc_mapping file contains binary-githash to gitbom-id mapping, helping user find
the associated bom-id which uniquely identifies the gitBOM artifact tree.
Package distributor can sign this bomsh_gitbom_doc_mapping file and distribute this file together with
the software package, or put this file in some repository or website for public access.
This is equivalent to embedding the bom-id into the binary file directly (online distribution vs. offline distribution).

The gitBOM docs for more build-reproducible packages can be added using the same approach.
More than 90% of Debian packages are build-reproducible (94.8% as of December 2021),
which means that the gitBOM docs can be generated for most of the already released Debian software packages.
Users of Linux distros can easily find the associated bom_id (as well as the whole gitBOM artifact tree) by querying our gitBOM repo for >90% of binary files in its Linux machine.
When the gitBOM docs for software packages are easily accessible, then people will be motivated to
utilize the gitBOM docs in our repo, and create more useful metadata (like CVE/bug/feature, etc.) to associate with the gitBOM artifact tree.
This will create a positive cycle for wide adoption of gitBOM.

The gitBOM repo is currently organized with below directory architecture:

    pkg_type/software_name/version/arch/objects/
    pkg_type/software_name/version/arch/metadata/

The objects directory stores the gitBOM docs, and metadata directory stores related metadata.
The top directory can be deb/rpm, or any applicable package types.
For example, deb/hostname/3.23/amd64/objects/ contains the gitBOM docs generated for hostname_3.23_amd64.deb package.
and deb/hostname/3.23/amd64/metadata/bomsh/ contains the metadata documents generated by bomsh for hostname_3.23_amd64.deb package.
A new tool foo can save its metadata under deb/hostname/3.23/amd64/metadata/foo/ directory.
For example, the CVE database for hostname can be saved under deb/hostname/3.23/amd64/metadata/CVE/ directory,
or if the CVE datatbase applies to all hostname versions, then it can be saved under deb/hostname/metatdata/CVE/ directory.
Or if the CVE database applies to both deb/rpm packages, then it can be saved under top level metadata/hostname/CVE/ directory.

The gitBOM docs of all software packages can put into a common objects directory for de-duplication.
Similar de-duplication efforts can be done for metadata. Other optimizations like compression can also be done.
Some access APIs will be provided, as in the SoftwareHeritage website. All these will be future work.

How to use this gitBOM repo
---------------------------

People can create various metadata and associate them with the gitBOM artifact tree.
For example, for CVE search, the bomsh_create_cve.py script from bomsh can be used to generate CVE database for OpenSSL.
Then the bomsh_search_cve.py script can be run to find out the vulnerable CVElist and FixedCVElist for your binary files.

    $ bomsh/scripts/bomsh_search_cve.py -b deb/openssl/1.1.1m-1/amd64 -d openssl_cvedb.json -m your-metadata-file -vv -f comma-separated-list-of-binaries

If you are just interested in inspecting the gitBOM artifact tree for your binary files, then you can use an empty cvedb file:

    $ echo "{}" > openssl_cvedb.json
    $ bomsh/scripts/bomsh_search_cve.py -b deb/openssl/1.1.1m-1/amd64 -d openssl_cvedb.json -m your-metadata-file -vv -f comma-separated-list-of-binaries

Similarly, bugs, features, security compliance, compatibility, build info, attestations, or declarations of mitigations can also be created as metadata.
If you have any good ideas, please share with us. More people involved, more useful gitBOM will be!
