project('not-a-wm', 'c',
    version: '0',
    meson_version: '>=0.39.1',
    license: [ 'MIT' ],
    default_options: [
        'c_std=gnu11',
        'warning_level=2',
    ],
)


headers = [
    'locale.h',
    'errno.h',
    'sys/mman.h',
    'stdbool.h',
    'sys/stat.h',
    'fcntl.h',
]
c_compiler = meson.get_compiler('c')
foreach h : headers
    if not c_compiler.has_header(h)
        error('Header @0@ was not found, but is required'.format(h))
    endif
endforeach

glib_min_major='2'
glib_min_minor='40'
glib_min_version='.'.join([glib_min_major, glib_min_minor])
wayland_min_version='1.12.92'
weston_supported_majors = [
    '3',
    '2',
]
last_weston_major = weston_supported_majors[weston_supported_majors.length() - 1]
weston_min_version='1.12.90'


wayland_server = dependency('wayland-server', version: '>= @0@'.format(wayland_min_version))

weston_major = ''
foreach try_weston_major : weston_supported_majors
    if weston_major == ''
        libweston = dependency('libweston-@0@'.format(try_weston_major), required: last_weston_major == try_weston_major)
        libweston_desktop = dependency('libweston-desktop-@0@'.format(try_weston_major), required: last_weston_major == try_weston_major)
        if libweston.found()
            weston_major = try_weston_major
        endif
    endif
endforeach
weston = dependency('weston')

header_conf = configuration_data()
header_conf.set_quoted('PACKAGE_NAME', meson.project_name())
header_conf.set_quoted('PACKAGE_VERSION', meson.project_version())
header_conf.set_quoted('GETTEXT_PACKAGE', meson.project_name())

header_conf.set_quoted('WESTON_PLUGINS_DIR', join_paths(weston.get_pkgconfig_variable('libdir'), 'weston'))
header_conf.set_quoted('LIBWESTON_PLUGINS_DIR', join_paths(libweston.get_pkgconfig_variable('libdir'), 'libweston-@0@'.format(weston_major)))

config_h = configure_file(output: 'config.h', configuration: header_conf)

executable('naw', [ config_h ] + files('main.c'),
    dependencies: [
        libweston,
        libweston_desktop,
        wayland_server,
    ]
)
