# CMake project file for a custom build of the libecc library
#
# This library is embedded in the bootloader of the WIZnet chip
#
# Two presets are available:
# - default: ARM Cortex-M0 embedded build
# - win: Windows build
#
# The Windows build produces a tool (ec_utils) that enables to generate keys,
# sign and verify signatures. The tool is required for the build of the
# application firmware, as part of the signing process.

cmake_minimum_required(VERSION 3.20)

project(libecc LANGUAGES C)

#-------------------------------------------------------------------------------
# libsign library
#-------------------------------------------------------------------------------

add_library(sign
    src/curves/aff_pt.c
    src/curves/aff_pt_edwards.c
    src/curves/aff_pt_montgomery.c
    src/curves/curves.c
    src/curves/ec_edwards.c
    src/curves/ec_montgomery.c
    src/curves/ec_params.c
    src/curves/ec_shortw.c
    src/curves/prj_pt.c
    src/ecdh/ecccdh.c
    src/ecdh/x25519_448.c
    src/fp/fp.c
    src/fp/fp_add.c
    src/fp/fp_montgomery.c
    src/fp/fp_mul.c
    src/fp/fp_mul_redc1.c
    src/fp/fp_pow.c
    src/fp/fp_rand.c
    src/fp/fp_sqrt.c
    src/hash/bash.c
    src/hash/bash224.c
    src/hash/bash256.c
    src/hash/bash384.c
    src/hash/bash512.c
    src/hash/belt-hash.c
    src/hash/hash_algs.c
    src/hash/hmac.c
    src/hash/ripemd160.c
    src/hash/sha224.c
    src/hash/sha256.c
    src/hash/sha3-224.c
    src/hash/sha3-256.c
    src/hash/sha3-384.c
    src/hash/sha3-512.c
    src/hash/sha3.c
    src/hash/sha384.c
    src/hash/sha512-224.c
    src/hash/sha512-256.c
    src/hash/sha512.c
    src/hash/sha512_core.c
    src/hash/shake.c
    src/hash/shake256.c
    src/hash/sm3.c
    src/hash/streebog.c
    src/nn/nn.c
    src/nn/nn_add.c
    src/nn/nn_div.c
    src/nn/nn_div.h
    src/nn/nn_logical.c
    src/nn/nn_modinv.c
    src/nn/nn_mod_pow.c
    src/nn/nn_mul.c
    src/nn/nn_mul.h
    src/nn/nn_mul_redc1.c
    src/nn/nn_rand.c
    src/sig/bign.c
    src/sig/bign_common.c
    src/sig/bip0340.c
    src/sig/dbign.c
    src/sig/decdsa.c
    src/sig/ecdsa.c
    src/sig/ecdsa_common.c
    src/sig/ecfsdsa.c
    src/sig/ecgdsa.c
    src/sig/eckcdsa.c
    src/sig/ecosdsa.c
    src/sig/ecrdsa.c
    src/sig/ecsdsa.c
    src/sig/ecsdsa_common.c
    src/sig/ec_key.c
    src/sig/eddsa.c
    src/sig/fuzzing_ecdsa.c
    src/sig/fuzzing_ecgdsa.c
    src/sig/fuzzing_ecrdsa.c
    src/sig/sig_algs.c
    src/sig/sm2.c
    src/utils/print_buf.c
    src/utils/print_curves.c
    src/utils/print_fp.c
    src/utils/print_keys.c
    src/utils/print_nn.c
    src/utils/utils.c
    src/utils/utils_rand.c
    src/utils/utils_rand.h
)

target_include_directories(sign PUBLIC
    include/
)

target_compile_definitions(sign PUBLIC
    WORDSIZE=32
    WITH_LIBECC_CONFIG_OVERRIDE
    WITH_CURVE_FRP256V1
    WITH_HASH_SHA256
    WITH_SIG_ECDSA
    WITH_STDLIB
    USE_SMALL_STACK
    NO_USE_COMPLETE_FORMULAS
)

if(WIN32)

target_compile_definitions(sign PRIVATE
    __WIN32__
)

endif()

if(ARM)

target_compile_options(sign PRIVATE
    -std=c99
    -mcpu=cortex-m0
    -mthumb
    -fmessage-length=0
    -ffunction-sections
    -fdata-sections
    -Wall
    -Wextra
    -pedantic
    -Os
)

endif(ARM)

#-------------------------------------------------------------------------------
# ec_utils tool
#-------------------------------------------------------------------------------

if(WIN32)

add_executable(ec_utils
    src/external_deps/print.c
    src/external_deps/rand.c
    src/external_deps/time.c
    src/tests/ec_utils.c
)

target_link_libraries(ec_utils sign)

target_compile_definitions(ec_utils PRIVATE
    __WIN32__
    _CRT_SECURE_NO_WARNINGS
)

endif()
