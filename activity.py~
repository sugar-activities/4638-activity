# Copyright 2013 Ceci Godoy - Samuel Reyes
#
# This program is free software; you can redistribute it and/or modify
# it under the terms of the GNU General Public License as published by
# the Free Software Foundation; either version 2 of the License, or
# (at your option) any later version.
#
# This program is distributed in the hope that it will be useful,
# but WITHOUT ANY WARRANTY; without even the implied warranty of
# MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
# GNU General Public License for more details.
#
# You should have received a copy of the GNU General Public License
# along with this program; if not, write to the Free Software
# Foundation, Inc., 51 Franklin St, Fifth Floor, Boston, MA  02110-1301  USA

"""CalculArteActivity: Calculadora que realiza operaciones basicas disenhada para personas con problemas de motricidad fisica."""

import gtk
import gobject
import logging

from gettext import gettext as _

from sugar.activity import activity
from sugar.graphics.toolbarbox import ToolbarBox
from sugar.activity.widgets import ActivityButton
from sugar.activity.widgets import ActivityToolbox
from sugar.activity.widgets import TitleEntry
from sugar.activity.widgets import StopButton
from sugar.activity.widgets import ShareButton




OPTIONS = ['0', '1', '2', '3', '4', '5', '6', '7', '8', '9', '+', '-', '*', '/', 'Borrar', '(', ')', 'Aceptar']
OPT_LENGHT = 5
OPT_FILAS = 4
DELAY = 1000


class CalculArteActivity(activity.Activity):
    """CalculArteActivity class as specified in activity.info"""

    def __init__(self, handle):
        """Set up the CalculArte activity."""
        activity.Activity.__init__(self, handle)

        # we do not have collaboration features
        # make the share option insensitive
        self.max_participants = 1

        # toolbar with the new toolbar redesign
        toolbar_box = ToolbarBox()

        activity_button = ActivityButton(self)
        toolbar_box.toolbar.insert(activity_button, 0)
        activity_button.show()

        title_entry = TitleEntry(self)
        toolbar_box.toolbar.insert(title_entry, -1)
        title_entry.show()

        share_button = ShareButton(self)
        toolbar_box.toolbar.insert(share_button, -1)
        share_button.show()
        
        separator = gtk.SeparatorToolItem()
        separator.props.draw = False
        separator.set_expand(True)
        toolbar_box.toolbar.insert(separator, -1)
        separator.show()

        stop_button = StopButton(self)
        toolbar_box.toolbar.insert(stop_button, -1)
        stop_button.show()

        self.set_toolbar_box(toolbar_box)
        toolbar_box.show()
        
        vbox = gtk.VBox()
        hbox = gtk.HBox(True)
        self.label_historial = gtk.Label()
        self.label_historial.set_line_wrap(True)
        self.entry_visor = gtk.Entry()
        self.table = gtk.Table(5,5, True)
        boton = gtk.Button('boton falso')
        self.label_historial.set_text('Ultimo resultado:')      
        self.set_canvas(hbox)
        hbox.add(vbox)
        hbox.add(self.label_historial)
        vbox.add(self.entry_visor)
        vbox.add(self.table)
        vbox.add(boton)

        OPTIONS.reverse()
        for j in range(4):
            for i in range(OPT_LENGHT):
                button = gtk.Button()
                option = OPTIONS.pop()
                button.set_label(option)
                button.connect('activate', self.__button_cb, self.entry_visor, boton, self.label_historial)
                self.connect('key-press-event', self.__barrido_columnas_cb)
                #canvas = self.get_canvas()
                #canvas.connect('key-press-event', self.__barrido_columnas_cb)
                if option == 'Aceptar':
                    self.table.attach(button, i, i+3, j, j+1)
                    break
                else:
                    self.table.attach(button, i, i+1, j, j+1)             
        hbox.show_all()
        boton.hide()
   
        OPTIONS.reverse()
        
        self._filas_index = 0
        self._columnas_index = 0
        self._flag_fila_columna = 0
        gobject.timeout_add(DELAY, self.__timeout_cb, self.table)


    def __button_cb(self, button, entry, boton, label):
        """ Callback que maneja el evento activate """
        
        if (button.get_label() == 'Aceptar'):
            try:
            
                resultado = str(eval(entry.get_text()))
                label.set_label('Ultimo resultado:\n' + entry.get_text() + ': ' + resultado)
            except ZeroDivisionError:
                label.set_label('Ultimo resultado:\n  Error: Division por cero [' +  entry.get_text()+']' )
                entry.set_text("")
                
            except:
                label.set_label('Ultimo resultado:\n Imposible de realizar la operacion. Intente nuevamente')
                entry.set_text("")
            
            
            entry.set_text("")
        elif (button.get_label() == 'Borrar'):
            entry.set_text(entry.get_text()[0:entry.get_text_length()-1])
          
        else:    
            entry.set_text(entry.get_text() + button.get_label())
        self._flag_fila_columna = 0
        self._filas_index = 0
        self._columnas_index = 0
        boton.grab_focus()


    def __timeout_cb(self, table):
        """ Callback que realiza el barrido automatico"""
        
        if self._flag_fila_columna == 0:
            self._filas_index = (self._filas_index) % OPT_FILAS
            buttons = table.get_children()
            buttons.reverse()
            buttons_focus = buttons[self._filas_index*OPT_LENGHT:self._filas_index*OPT_LENGHT+OPT_LENGHT]
            for button in buttons:
                button.modify_bg(gtk.STATE_NORMAL, gtk.gdk.Color('dark gray'))
            for button in buttons_focus:
                button.modify_bg(gtk.STATE_NORMAL, gtk.gdk.Color('light gray'))
            self._filas_index += 1
        else: 
            
            buttons = table.get_children()
            buttons.reverse()

            if self._filas_index == 4:
                self._columnas_index = (self._columnas_index) % 3
                buttons_focus = buttons[(self._filas_index-1)*OPT_LENGHT:(self._filas_index-1)*OPT_LENGHT+3]
            else:
                self._columnas_index = (self._columnas_index) % OPT_LENGHT
                buttons_focus = buttons[(self._filas_index-1)*OPT_LENGHT:(self._filas_index-1)*OPT_LENGHT+OPT_LENGHT]
                
            button = buttons_focus[self._columnas_index]
            button.grab_focus()
            
            self._columnas_index += 1

        return True
    
    def __barrido_columnas_cb(self, table, arg):
        """ Callback para cambiar valor del flag que verifica si corresponde a una fila o columna"""
        self._flag_fila_columna = 1
        
        
    def read_file(self, tmp_file):

        # resume metadata
        try:
            self.label_historial.set_label(self.metadata['entry'])
            self._filas_index = int(self.metadata['fila']) 
            self._columnas_index = int(self.metadata['columna'])
            self._flag_fila_columna = int(self.metadata['flag'])
            buttons = self.table.get_children()
            buttons.reverse()
            buttons_focus = buttons[(self._filas_index-1)*OPT_LENGHT:(self._filas_index-1)*OPT_LENGHT+OPT_LENGHT]
            for button in buttons:
                button.modify_bg(gtk.STATE_NORMAL, gtk.gdk.Color('dark gray'))
            for button in buttons_focus:
                button.modify_bg(gtk.STATE_NORMAL, gtk.gdk.Color('light gray'))
        except KeyError:
            logging.error("No entry metadata")

        # resume data
        data = open(tmp_file, "r")
        buffer = self.entry_visor
        buffer.set_text(data.read())
        data.close()

    def write_file(self, tmp_file):

        # save metadata
        self.metadata['entry'] = self.label_historial.get_label()
        self.metadata['fila'] = self._filas_index
        self.metadata['columna'] = self._columnas_index
        self.metadata['flag'] = self._flag_fila_columna
        
        # save data
        data = open(tmp_file, "w")
        buffer = self.entry_visor.get_text()
        data.write(buffer)
        data.close()
