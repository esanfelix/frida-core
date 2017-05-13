interfaces_sources = [
  'session.vala',
]

interfaces = library('frida-interfaces', interfaces_sources,
  dependencies: [glib_dep, gobject_dep, gio_dep],
)

interfaces_dep = declare_dependency(
  include_directories: include_directories('.'),
  link_with: interfaces,
)

interfaces_build_dir = meson.current_build_dir()
