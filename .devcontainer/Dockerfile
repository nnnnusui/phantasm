ARG VARIANT="hirsute"
FROM mcr.microsoft.com/vscode/devcontainers/base:0-${VARIANT}

WORKDIR /tmp

# Install apps
  RUN apt update
  # required for LLVM
    RUN apt install -y software-properties-common
    RUN wget https://apt.llvm.org/llvm.sh
    RUN chmod +x llvm.sh
  # required for EDK2/edksetup.sh
    RUN apt install -y python3-distutils make nasm
  # required for EDK2/Ovmf
    RUN apt install -y iasl
  # required for EDK2/BaseTools
    RUN apt install -y gcc g++ uuid-dev

# LLVM
  ARG version_llvm
  RUN ./llvm.sh ${version_llvm}
  RUN ln -s -f /usr/bin/clang-${version_llvm} /usr/bin/clang
  RUN ln -s -f /usr/bin/llvm-ar-${version_llvm} /usr/bin/llvm-ar
  RUN ln -s -f /usr/bin/llvm-rc-${version_llvm} /usr/bin/llvm-rc
  RUN ln -s -f /usr/bin/llvm-lib-${version_llvm} /usr/bin/llvm-lib
  RUN ln -s -f /usr/bin/lld-link-${version_llvm} /usr/bin/lld-link

# Build workspace
  ARG work_user
  USER ${work_user}
  ARG work_path
  ENV workspace=${work_path}
WORKDIR ${workspace}

# EDK2
  ARG version_edk2
  ENV edk2_architecture="X64"
  ENV edk2_tool="CLANGPDB"
  RUN git clone https://github.com/tianocore/edk2.git edk2 -b ${version_edk2}
  RUN cd edk2 && git submodule update --init
  # Build BaseTools
    RUN make -C edk2/BaseTools/Source/C
  # Build Ovmf
    SHELL ["/bin/bash", "-c"]
    RUN cd edk2 \
      && source edksetup.sh \
      && build -a ${edk2_architecture} -t ${edk2_tool} -p "OvmfPkg/OvmfPkgX64.dsc"
    SHELL ["/bin/sh", "-c"]
  RUN cp edk2/Build/Ovmf${edk2_architecture}/DEBUG_${edk2_tool}/FV/OVMF_CODE.fd .
  RUN cp edk2/Build/Ovmf${edk2_architecture}/DEBUG_${edk2_tool}/FV/OVMF_VARS.fd .

WORKDIR ${workspace}/reference
# mikanos
  RUN git clone https://github.com/uchan-nos/mikanos-build.git osbook
  RUN git clone https://github.com/uchan-nos/mikanos.git mikanos
  RUN cd mikanos && git checkout osbook_day03c
  RUN ln -s `pwd`/mikanos/MikanLoaderPkg ${workspace}/edk2/
