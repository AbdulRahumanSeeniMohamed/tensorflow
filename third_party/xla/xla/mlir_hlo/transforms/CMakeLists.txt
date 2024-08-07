#
# Copyright 2021 The OpenXLA Authors.
#
# Licensed under the Apache License, Version 2.0 (the "License");
# you may not use this file except in compliance with the License.
# You may obtain a copy of the License at
#
#      https://www.apache.org/licenses/LICENSE-2.0
#
# Unless required by applicable law or agreed to in writing, software
# distributed under the License is distributed on an "AS IS" BASIS,
# WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
# See the License for the specific language governing permissions and
# limitations under the License.
#

set(LLVM_TARGET_DEFINITIONS passes.td)
mlir_tablegen(passes.h.inc -gen-pass-decls -name LMHLOTransforms)
add_public_tablegen_target(LMHLOTransformsPassIncGen)

set(LLVM_TARGET_DEFINITIONS gpu_passes.td)
mlir_tablegen(gpu_passes.h.inc -gen-pass-decls -name LMHLOGPUTransforms)
add_public_tablegen_target(LMHLOGPUTransformsPassIncGen)

add_mlir_library(MLIRBufferTransforms
  alloc_to_arg_pass.cc
  bufferize.cc
  bufferize_pass.cc
  collapse_parallel_loops_to_1d_pass.cc
  detensorize_scf_ops.cc
  generic_host_to_llvm.cc
  lower_index_cast_pass.cc
  naive_copy_removal.cc
  tile_loops_pass.cc
  vectorize_copy.cc
  unbufferize_pass.cc

  DEPENDS
  LMHLOTransformsPassIncGen
  MLIRDeallocationPassesIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  ChloOps
  MLIRGPUDialect
  MLIRIR
  MLIRMathTransforms
  MLIRPass
  MLIRReconcileUnrealizedCasts
  MLIRShapeDialect
  MLIRTransformDialectTransforms
  MLIRTransforms
  MLIRX86VectorDialect
  MLIRX86VectorTransforms
  MhloDialect
)

add_mlir_library(MLIRHLOGPUTransforms
  gpu_kernel_lowering_passes.cc
  gpu_passes.cc

  DEPENDS
  LMHLOGPUTransformsPassIncGen

  LINK_COMPONENTS
  Core

  LINK_LIBS PUBLIC
  MLIRArithTransforms
  MLIRGPUDialect
  MLIRIR
  MLIRMemRefTransforms
  MLIRPass
  MLIRShapeDialect
  MLIRSCFTransforms
  MLIRTransforms
  MLIRVectorTransforms
)
