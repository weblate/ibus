# ibus-engine-simple
ibus_engine_simple_sources = files(
  'main.vala',
)

ibus_engine_simple_deps = [
  glib_dep,
  gio_dep,
  ibus_dep,
  ibus_vapi,
]

ibus_engine_simple_cflags = [
  '-DG_LOG_DOMAIN="@0@"'.format(ibus_log_domain),
]

ibus_engine_simple = executable('ibus-engine-simple',
  ibus_engine_simple_sources,
  dependencies: ibus_engine_simple_deps,
  c_args: ibus_engine_simple_cflags,
  install: true,
  install_dir: get_option('libexecdir'),
)

# simple.xml
if fs.exists(meson.current_source_dir() / 'simple.xml.in')
  simple_xml_in = files('simple.xml.in')
else
  simple_xml_in = custom_target(
    'simple.xml.in',
    output: 'simple.xml.in',
    command: [
      python,
      find_program('gensimple.py'),
      '--input', xkbconfig_base / 'rules' / 'evdev.xml',
      '--output=@OUTPUT@',
      '--version', meson.project_version(),
      '--exec-path', '@libexecdir@/ibus-engine-simple',
      '--iso-path',
        isocodes_prefix / 'share' / 'xml' / 'iso-codes' / 'iso_639.xml',
      '--first-language',
    ],
  )
endif

simple_xml = custom_target(
  'simple.xml',
  input: simple_xml_in,
  output: '@BASENAME@',
  command: [
    python, '-c', '''
import sys
infile, outfile, libexec = sys.argv[1], sys.argv[2], sys.argv[3]
with open(infile, "r") as f:
    content = f.read()
content = content.replace("@libexecdir@", libexec)
with open(outfile, "w") as f:
    f.write(content)
''',
    '@INPUT@',
    '@OUTPUT@',
    get_option('prefix') / get_option('libexecdir'),
  ],
  install: true,
  install_dir: pkgdatadir / 'component',
)

distro_tests_env = {
  'lib_srcdir': source_root / 'src',
}

if get_option('tests') and get_option('distro-tests')
  distro_tests = [
    { 'name': 'test-dead', },
    { 'name': 'test-gnome', },
  ]

  foreach _test : distro_tests
    timeout = _test.get('timeout', 30)
    test(_test['name'], files([python.full_path()])[0],
      args: [ meson.current_source_dir() / '@0@.py'.format(_test['name']) ],
      env: environment(distro_tests_env),
      suite: 'engine',
      timeout: timeout,
    )
  endforeach
endif
