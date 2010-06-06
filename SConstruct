# vim: ft=python expandtab

import os
from site_init import *

opts = Variables()
opts.Add(PathVariable('PREFIX', 'Installation prefix', os.path.expanduser('~/FOSS'), PathVariable.PathIsDirCreate))
opts.Add(BoolVariable('DEBUG', 'Build with Debugging information', 0))
opts.Add(PathVariable('PERL', 'Path to the executable perl', r'C:\Perl\bin\perl.exe', PathVariable.PathIsFile))

env = Environment(variables = opts,
                  ENV=os.environ, tools = ['default', GBuilder])

Initialize(env)

DBUS_GLIB_MAJOR_VERSION=0
DBUS_GLIB_MINOR_VERSION=86
DBUS_GLIB_VERSION_STRING="%d.%d" % (DBUS_GLIB_MAJOR_VERSION, DBUS_GLIB_MINOR_VERSION)

env['DOT_IN_SUBS'] = {'@PACKAGE_VERSION@': DBUS_GLIB_VERSION_STRING,
                      '@VERSION@': DBUS_GLIB_VERSION_STRING,
                      '@DBUS_GLIB_VERSION@': DBUS_GLIB_VERSION_STRING,
                      '@DBUS_GLIB_MAJOR_VERSION@': str(DBUS_GLIB_MAJOR_VERSION),
                      '@DBUS_GLIB_MINOR_VERSION@': str(DBUS_GLIB_MINOR_VERSION),
                      '@prefix@': env['PREFIX'],
                      '@exec_prefix@': '${prefix}/bin',
                      '@libdir@': '${prefix}/lib',
                      '@includedir@': '${prefix}/include'}

env.ParseConfig('pkg-config gobject-2.0 --cflags --libs')
env.ParseConfig('pkg-config dbus-1 --cflags --libs')
env.Append(CPPPATH=['#'])
env.Append(CFLAGS=env['DEBUG_CFLAGS'])
env.Append(CPPDEFINES=env['DEBUG_CPPDEFINES'])

env.DotIn('dbus-glib-1.pc', 'dbus-glib-1.pc.in')
env.Alias('install', env.Install('$PREFIX/lib/pkgconfig', ['dbus-glib-1.pc']))
env['DOT_IN_SUBS']['@PCS@']=generate_file_element('dbus-glib-1.pc', r'lib/pkgconfig', env)

env.DotIn('config.h', 'config.h.win32.in')
env.Command('config.h.win32.in', ['config.h.in', 'config.h.in.win32.patch'], '''    copy /y config.h.in config.h.win32.in
    patch < config.h.in.win32.patch''')

SConscript(['dbus/SConscript'], exports='env')
