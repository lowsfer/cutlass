# Copyright (c) 2017-2018, NVIDIA CORPORATION.  All rights reserved.
#
# Redistribution and use in source and binary forms, with or without modification, are permitted
# provided that the following conditions are met:
#     * Redistributions of source code must retain the above copyright notice, this list of
#       conditions and the following disclaimer.
#     * Redistributions in binary form must reproduce the above copyright notice, this list of
#       conditions and the following disclaimer in the documentation and/or other materials
#       provided with the distribution.
#     * Neither the name of the NVIDIA CORPORATION nor the names of its contributors may be used
#       to endorse or promote products derived from this software without specific prior written
#       permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS "AS IS" AND ANY EXPRESS OR
# IMPLIED WARRANTIES, INCLUDING, BUT NOT LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND
# FITNESS FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL NVIDIA CORPORATION BE LIABLE
# FOR ANY DIRECT, INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES (INCLUDING,
# BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR SERVICES; LOSS OF USE, DATA, OR PROFITS;
# OR BUSINESS INTERRUPTION) HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT,
# STRICT LIABILITY, OR TOR (INCLUDING NEGLIGENCE OR OTHERWISE) ARISING IN ANY WAY OUT OF THE USE
# OF THIS SOFTWARE, EVEN IF ADVISED OF THE POSSIBILITY OF SUCH DAMAGE.


include_directories(
  ../../external/googletest/googletest
  .
)

link_directories(
  ../../external/googletest/googletest
)

link_libraries(
    gtest
)

set(CUTLASS_UNIT_TEST_HEADERS
  cutlass_unit_test.h
  core/layout_verification.h
)

set(CUTLASS_UNIT_TEST_SOURCES
  cutlass_unit_test.cpp
  util/host_tensor.cu
  core/layout_verification.cu
  core/predicate_vector.cu
  core/tile_iterator.cu
  gemm/dgemm.cu
  gemm/hgemm_128x128x8.cu
  gemm/hgemm_128x128x16.cu
  gemm/hgemm_128x32x8.cu
  gemm/hgemm_128x64x8.cu
  gemm/igemm_128x128x32.cu
  gemm/igemm_128x64x32.cu
  gemm/igemm_128x32x32.cu
  gemm/igemm_128x128x32_float.cu
  gemm/igemm_128x128x32_int8.cu
  gemm/igemm_32x32x128.cu
  gemm/sgemm_128x128x8.cu
  gemm/sgemm_128x128x16.cu
  gemm/sgemm_128x64x8.cu
  gemm/sgemm_128x64x16.cu
  gemm/sgemm_128x32x8.cu
  gemm/sgemm_128x32x16.cu
  gemm/sgemm_64x128x8.cu
  gemm/sgemm_64x128x16.cu
  gemm/sgemm_64x64x8.cu
  gemm/sgemm_64x64x16.cu
  gemm/sgemm_64x32x8.cu
  gemm/sgemm_64x32x16.cu
  gemm/wmma_gemm.cu
)

if (CUTLASS_NVRTC_ENABLE)
  list(APPEND CUTLASS_UNIT_TEST_SOURCES gemm/gemm_nvrtc.cu)
endif()

source_group("Source\ Files" FILES ${CUTLASS_UNIT_TEST_SOURCES})

if (NOT CUTLASS_NATIVE_CUDA)
  # cuda_add_executable does not take interface include directories into account
  # Let's fetch them and pass them to CUDA.
  get_target_property(CUTLASS_INCLUDES CUTLASS INTERFACE_INCLUDE_DIRECTORIES)
  include_directories("${CUTLASS_INCLUDES}")
endif()

cutlass_add_executable(
  cutlass_unit_test
  ${CUTLASS_UNIT_TEST_SOURCES}
  ${CUTLASS_UNIT_TEST_HEADERS}
)

if (CUTLASS_NVRTC_ENABLE)
  target_link_libraries(cutlass_unit_test
    cutlass_nvrtc
    cuda
    )

  if (WIN32)
    target_link_libraries(cutlass_unit_test Dbghelp)
  endif()
endif()

CUDA_ADD_CUBLAS_TO_TARGET(cutlass_unit_test)
