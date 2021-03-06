#
# SPDX-License-Identifier: BSD-3-Clause
#
# Copyright © 2019 Keith Packard
#
# Redistribution and use in source and binary forms, with or without
# modification, are permitted provided that the following conditions
# are met:
#
# 1. Redistributions of source code must retain the above copyright
#    notice, this list of conditions and the following disclaimer.
#
# 2. Redistributions in binary form must reproduce the above
#    copyright notice, this list of conditions and the following
#    disclaimer in the documentation and/or other materials provided
#    with the distribution.
#
# 3. Neither the name of the copyright holder nor the names of its
#    contributors may be used to endorse or promote products derived
#    from this software without specific prior written permission.
#
# THIS SOFTWARE IS PROVIDED BY THE COPYRIGHT HOLDERS AND CONTRIBUTORS
# "AS IS" AND ANY EXPRESS OR IMPLIED WARRANTIES, INCLUDING, BUT NOT
# LIMITED TO, THE IMPLIED WARRANTIES OF MERCHANTABILITY AND FITNESS
# FOR A PARTICULAR PURPOSE ARE DISCLAIMED. IN NO EVENT SHALL THE
# COPYRIGHT HOLDER OR CONTRIBUTORS BE LIABLE FOR ANY DIRECT,
# INDIRECT, INCIDENTAL, SPECIAL, EXEMPLARY, OR CONSEQUENTIAL DAMAGES
# (INCLUDING, BUT NOT LIMITED TO, PROCUREMENT OF SUBSTITUTE GOODS OR
# SERVICES; LOSS OF USE, DATA, OR PROFITS; OR BUSINESS INTERRUPTION)
# HOWEVER CAUSED AND ON ANY THEORY OF LIABILITY, WHETHER IN CONTRACT,
# STRICT LIABILITY, OR TORT (INCLUDING NEGLIGENCE OR OTHERWISE)
# ARISING IN ANY WAY OUT OF THE USE OF THIS SOFTWARE, EVEN IF ADVISED
# OF THE POSSIBILITY OF SUCH DAMAGE.
#
machine_dirs = {
		 'a29k': 'a29k',
		 'aarch64': 'aarch64',
		 'arc': 'arc',
		 'arm': 'arm',
		 'bfin': 'bfin',
		 'cr16': 'cr16',
		 'cris': 'cris',
		 'crisv32': 'cris',
		 'crx': 'crx',
		 'd10v': 'd10v',
		 'd30v': 'd30v',
		 'epiphany': 'epiphany',
		 'fido': 'm68k',
		 'fr30': 'fr30',
		 'frv': 'frv',
		 'ft32': 'ft32',
		 'h8300': 'h8300',
		 'h8500': 'h8500',
		 'hppa': 'hppa',
		 'i960': 'i960',
		 'i386': 'i386',
		 'i486': 'i386',
		 'i586': 'i386',
		 'i686': 'i386',
		 'iq2000': 'iq2000',
		 'lm32': 'lm32',
		 'm32c': 'm32c',
		 'm32r': 'm32r',
		 'm6811': 'm68hc11',
		 'm6812': 'm68hc11',
		 'm68hc11': 'm68hc11',
		 'm68hc12': 'm68hc11',
		 'm68k': 'm68k',
		 'm88k': 'm88k',
		 'm88110': 'm88k',
		 'microblaze': 'microblaze',
		 'mep': 'mep',
		 'mips': 'mips',
		 'mn10200': 'mn10200',
		 'mn10300': 'mn10300',
		 'moxie': 'moxie',
		 'msp430': 'msp430',
		 'mt': 'mt',
		 'nds32': 'nds32',
		 'nios2': 'nios2',
		 'nvptx': 'nvptx',
		 'or1k': 'or1k',
		 'or1knd': 'or1k',
		 'powerpc': 'powerpc',
		 'riscv': 'riscv',
		 'riscv32': 'riscv',
		 'riscv64': 'riscv',
		 'rl78': 'rl78',
		 'rx': 'rx',
		 'sh': 'sh',
		 'sh64': 'sh',
		 'sparc': 'sparc',
		 'c4x': 'tic4x',
		 'tic4x': 'tic4x',
		 'tic6x': 'tic6x',
		 'tic80': 'tic80',
		 'v850': 'v850',
		 'visium': 'visium',
		 'w65': 'w65',
		 'x86_64': 'x86_64',
		 'xc16x': 'xc16x',
		 'xstormy16': 'xstormy16',
		 'xtensa': 'xtensa',
		 'z8k': 'z8k',
		 'spu': 'spu',
	       }

libdirs = ['argz', 'ctype', 'errno', 'iconv', 'misc', 'search',
	   'signal', 'ssp', 'stdlib', 'string', 'time',
	   'xdr', 'locale']

if enable_picolib
  libdirs += 'picolib'
endif

if newlib_tinystdio
  libdirs += 'tinystdio'
else
  libdirs += ['stdio', 'reent']
  if newlib_stdio64
    libdirs += 'stdio64'
  endif
endif

if newlib_supplied_syscalls
  libdirs += 'syscalls'
endif

libnames = libdirs

need_generic_fenv_h = true

if machine_dirs.has_key(host_cpu_family)
  machine_dir = join_paths('machine', machine_dirs[host_cpu_family])
  fenv_file_check = join_paths(machine_dir, 'sys', 'fenv.h')
  if run_command('test', '-f', fenv_file_check).returncode() == 0
    need_generic_fenv_h = false
  else
    need_generic_fenv_h = true
  endif

  machine_inc_dir = join_paths(machine_dir, 'include')

  if run_command('test', '-d', machine_inc_dir).returncode() == 0
    inc = [include_directories(machine_inc_dir), inc]
  endif

  inc = [include_directories(machine_dir), inc]

  libdirs = [machine_dir] + libdirs
  libnames = ['machine'] + libnames
else
  warning('No libc machine directory for CPU family ' + host_cpu_family)
  need_generic_fenv_h = true
endif

foreach libdir : libdirs
  subdir(libdir)
endforeach

subdir('include')

foreach target : targets
  value = get_variable('target_' + target)
  libobjs = []

  foreach libname : libnames
    libobjs += get_variable('lib_' + libname + target).extract_all_objects(recursive:true)
  endforeach

  instdir = join_paths(get_option('libdir'), value[0])

  if target == ''
    libc_name = 'c'
  else
    libc_name = join_paths(target, 'libc')
  endif

  set_variable('lib_c' + target,
	       static_library(libc_name,
			      install : true,
			      install_dir : instdir,
			      pic: false,
			      objects : libobjs))
endforeach

