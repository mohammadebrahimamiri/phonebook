from kivy.app import App
from kivy.uix.boxlayout import BoxLayout
from kivy.uix.gridlayout import GridLayout
from kivy.uix.scrollview import ScrollView
from kivy.uix.textinput import TextInput
from kivy.uix.button import Button
from kivy.uix.label import Label
from kivy.uix.popup import Popup
from kivy.core.window import Window
from kivy.utils import get_color_from_hex
from kivy.graphics import Color, Rectangle
from kivy.config import Config
import json
import os

# Configure app
Config.set('kivy', 'window_icon', '')
Config.set('graphics', 'width', '500')
Config.set('graphics', 'height', '700')
Window.clearcolor = get_color_from_hex('#f5f5f5')  # Light gray background

class RoundedButton(Button):
    def __init__(self, **kwargs):
        super(RoundedButton, self).__init__(**kwargs)
        self.background_normal = ''
        self.background_down = ''
        self.background_color = get_color_from_hex('#3498db')
        self.border = (5, 5, 5, 5)

class StylishLabel(Label):
    def __init__(self, **kwargs):
        super(StylishLabel, self).__init__(**kwargs)
        self.color = get_color_from_hex('#34495e')  # Dark blue text

class StylishTextInput(TextInput):
    def __init__(self, **kwargs):
        super(StylishTextInput, self).__init__(**kwargs)
        self.background_normal = ''
        self.background_active = ''
        self.padding = [10, 10, 10, 0]
        self.background_color = get_color_from_hex('#ffffff')
        self.cursor_color = get_color_from_hex('#2980b9')
        self.foreground_color = get_color_from_hex('#34495e')

class ContactCard(BoxLayout):
    def __init__(self, contact, delete_callback, edit_callback, **kwargs):
        super(ContactCard, self).__init__(**kwargs)
        self.orientation = 'vertical'
        self.size_hint_y = None
        self.height = 120
        self.padding = [10, 5, 10, 5]
        self.spacing = 5
        
        # Add background with rounded corners
        with self.canvas.before:
            Color(rgba=get_color_from_hex('#ffffff'))
            self.rect = Rectangle(pos=self.pos, size=self.size)
        
        self.bind(pos=self.update_rect, size=self.update_rect)
        
        # Contact information
        info_box = BoxLayout(orientation='vertical', padding=[10, 5])
        
        name_label = StylishLabel(
            text=f"Name: {contact['name']}",
            font_size='16sp',
            halign='left',
            text_size=(400, None),
            bold=True
        )
        
        phone_label = StylishLabel(
            text=f"Phone: {contact['phone']}",
            font_size='14sp',
            halign='left',
            text_size=(400, None)
        )
        
        info_box.add_widget(name_label)
        info_box.add_widget(phone_label)
        
        # Action buttons
        action_box = BoxLayout(size_hint_y=None, height=40, spacing=10, padding=[0, 5, 0, 5])
        
        edit_btn = RoundedButton(
            text='Edit',
            size_hint_x=0.5,
            background_color=get_color_from_hex('#2ecc71')  # Green
        )
        edit_btn.bind(on_press=lambda x: edit_callback(contact))
        
        delete_btn = RoundedButton(
            text='Delete',
            size_hint_x=0.5,
            background_color=get_color_from_hex('#e74c3c')  # Red
        )
        delete_btn.bind(on_press=lambda x: delete_callback(contact))
        
        action_box.add_widget(edit_btn)
        action_box.add_widget(delete_btn)
        
        self.add_widget(info_box)
        self.add_widget(action_box)
    
    def update_rect(self, instance, value):
        self.rect.pos = instance.pos
        self.rect.size = instance.size

class PhoneBookApp(App):
    def build(self):
        self.title = 'Modern Phone Book'
        self.icon = 'icon.png'  # Add an icon file to your directory if you have one
        
        # Load contacts
        self.contacts = self.load_contacts()
        self.current_contact = None
        
        # Main layout
        main_layout = BoxLayout(orientation='vertical', padding=15, spacing=10)
        
        # App title
        title_box = BoxLayout(size_hint_y=None, height=60)
        title_label = StylishLabel(
            text='PHONE BOOK',
            font_size='24sp',
            bold=True,
            color=get_color_from_hex('#2c3e50')
        )
        title_box.add_widget(title_label)
        main_layout.add_widget(title_box)
        
        # Input section
        input_layout = GridLayout(cols=2, size_hint_y=None, height=150, spacing=[10, 15])
        input_layout.padding = [20, 20, 20, 20]
        
        # Add background with rounded corners
        with input_layout.canvas.before:
            Color(rgba=get_color_from_hex('#ecf0f1'))  # Light gray background
            self.input_rect = Rectangle(pos=input_layout.pos, size=input_layout.size)
        
        input_layout.bind(pos=self.update_rect, size=self.update_rect)
        
        # Name input
        name_label = StylishLabel(text='Name:', size_hint_x=0.3, halign='right')
        self.name_input = StylishTextInput(
            hint_text='Enter name',
            multiline=False,
            size_hint_x=0.7
        )
        
        # Phone input
        phone_label = StylishLabel(text='Phone:', size_hint_x=0.3, halign='right')
        self.phone_input = StylishTextInput(
            hint_text='Enter phone number',
            multiline=False,
            size_hint_x=0.7
        )
        
        input_layout.add_widget(name_label)
        input_layout.add_widget(self.name_input)
        input_layout.add_widget(phone_label)
        input_layout.add_widget(self.phone_input)
        
        main_layout.add_widget(input_layout)
        
        # Buttons section
        buttons_layout = BoxLayout(size_hint_y=None, height=50, spacing=10, padding=[10, 5, 10, 5])
        
        self.save_button = RoundedButton(
            text='Save Contact',
            background_color=get_color_from_hex('#2ecc71')  # Green
        )
        self.save_button.bind(on_press=self.save_contact)
        
        clear_button = RoundedButton(
            text='Clear',
            background_color=get_color_from_hex('#95a5a6')  # Gray
        )
        clear_button.bind(on_press=self.clear_inputs)
        
        search_button = RoundedButton(
            text='Search',
            background_color=get_color_from_hex('#3498db')  # Blue
        )
        search_button.bind(on_press=self.search_contacts)
        
        buttons_layout.add_widget(self.save_button)
        buttons_layout.add_widget(clear_button)
        buttons_layout.add_widget(search_button)
        
        main_layout.add_widget(buttons_layout)
        
        # Status label
        self.status_label = StylishLabel(
            text='',
            size_hint_y=None,
            height=40,
            color=get_color_from_hex('#27ae60')  # Green for success messages
        )
        main_layout.add_widget(self.status_label)
        
        # Contacts list section
        list_label = StylishLabel(
            text='Contacts',
            size_hint_y=None,
            height=40,
            font_size='18sp',
            bold=True
        )
        main_layout.add_widget(list_label)
        
        # Scrollable contacts list
        self.scroll_view = ScrollView()
        self.contacts_layout = GridLayout(
            cols=1,
            spacing=10,
            size_hint_y=None,
            padding=[5, 5]
        )
        self.contacts_layout.bind(minimum_height=self.contacts_layout.setter('height'))
        
        self.scroll_view.add_widget(self.contacts_layout)
        main_layout.add_widget(self.scroll_view)
        
        # Refresh contacts list
        self.refresh_contacts_list()
        
        return main_layout
    
    def update_rect(self, instance, value):
        self.input_rect.pos = instance.pos
        self.input_rect.size = instance.size
    
    def load_contacts(self):
        # Load contacts from file
        if os.path.exists('contacts.json'):
            try:
                with open('contacts.json', 'r') as f:
                    return json.load(f)
            except Exception as e:
                print(f"Error loading contacts: {e}")
        return []
    
    def save_contacts_to_file(self):
        # Save contacts to file
        try:
            with open('contacts.json', 'w') as f:
                json.dump(self.contacts, f, indent=4)
        except Exception as e:
            print(f"Error saving contacts: {e}")
            self.show_error_popup(f"Could not save contacts: {e}")
    
    def refresh_contacts_list(self, search_query=None):
        # Clear current list
        self.contacts_layout.clear_widgets()
        
        # Filter contacts if search query is provided
        displayed_contacts = self.contacts
        if search_query:
            search_query = search_query.lower()
            displayed_contacts = [
                contact for contact in self.contacts
                if search_query in contact['name'].lower() or search_query in contact['phone'].lower()
            ]
        
        # Show message if no contacts
        if not displayed_contacts:
            no_contacts_label = StylishLabel(
                text='No contacts found',
                size_hint_y=None,
                height=50
            )
            self.contacts_layout.add_widget(no_contacts_label)
        else:
            # Add contact cards
            for contact in displayed_contacts:
                contact_card = ContactCard(
                    contact=contact,
                    delete_callback=self.confirm_delete_contact,
                    edit_callback=self.edit_contact,
                    size_hint_y=None,
                    height=120
                )
                self.contacts_layout.add_widget(contact_card)
        
        # Update layout height
        self.contacts_layout.height = len(displayed_contacts) * 130 if displayed_contacts else 50
    
    def save_contact(self, instance):
        name = self.name_input.text.strip()
        phone = self.phone_input.text.strip()
        
        if not name or not phone:
            self.status_label.text = 'Please enter both name and phone number'
            self.status_label.color = get_color_from_hex('#e74c3c')  # Red for error
            return
        
        # Validate phone number
        if not all(c.isdigit() or c in '+-() ' for c in phone):
            self.status_label.text = 'Phone number can only contain digits and +-() characters'
            self.status_label.color = get_color_from_hex('#e74c3c')
            return
        
        # Check if we're updating an existing contact
        if self.current_contact:
            # Find and update the contact
            for contact in self.contacts:
                if contact == self.current_contact:
                    contact['name'] = name
                    contact['phone'] = phone
                    self.current_contact = None
                    self.save_button.text = 'Save Contact'
                    self.status_label.text = f'Contact updated successfully'
                    self.status_label.color = get_color_from_hex('#27ae60')  # Green
                    break
        else:
            # Check for duplicate phone number
            for contact in self.contacts:
                if contact['phone'] == phone:
                    self.status_label.text = 'A contact with this phone number already exists'
                    self.status_label.color = get_color_from_hex('#e74c3c')
                    return
            
            # Add new contact
            self.contacts.append({
                'name': name,
                'phone': phone
            })
            self.status_label.text = 'Contact added successfully'
            self.status_label.color = get_color_from_hex('#27ae60')
        
        # Save to file and refresh list
        self.save_contacts_to_file()
        self.refresh_contacts_list()
        self.clear_inputs(None)
    
    def edit_contact(self, contact):
        self.current_contact = contact
        self.name_input.text = contact['name']
        self.phone_input.text = contact['phone']
        self.save_button.text = 'Update Contact'
        self.status_label.text = f'Editing contact: {contact["name"]}'
        self.status_label.color = get_color_from_hex('#3498db')  # Blue
    
    def confirm_delete_contact(self, contact):
        # Create confirm dialog
        content = BoxLayout(orientation='vertical', padding=10, spacing=10)
        
        message = StylishLabel(
            text=f'Are you sure you want to delete "{contact["name"]}"?',
            size_hint_y=None,
            height=60
        )
        
        buttons = BoxLayout(size_hint_y=None, height=40, spacing=10)
        
        yes_btn = RoundedButton(
            text='Yes, Delete',
            background_color=get_color_from_hex('#e74c3c')
        )
        
        no_btn = RoundedButton(
            text='Cancel',
            background_color=get_color_from_hex('#7f8c8d')
        )
        
        buttons.add_widget(yes_btn)
        buttons.add_widget(no_btn)
        
        content.add_widget(message)
        content.add_widget(buttons)
        
        popup = Popup(
            title='Confirm Deletion',
            content=content,
            size_hint=(0.8, 0.3),
            auto_dismiss=False
        )
        
        yes_btn.bind(on_press=lambda x: self.delete_contact(contact, popup))
        no_btn.bind(on_press=popup.dismiss)
        
        popup.open()
    
    def delete_contact(self, contact, popup):
        self.contacts.remove(contact)
        self.save_contacts_to_file()
        self.refresh_contacts_list()
        
        self.status_label.text = f'Contact "{contact["name"]}" deleted'
        self.status_label.color = get_color_from_hex('#e74c3c')  # Red
        
        # If we were editing this contact, clear the form
        if self.current_contact == contact:
            self.clear_inputs(None)
        
        popup.dismiss()
    
    def clear_inputs(self, instance):
        self.name_input.text = ''
        self.phone_input.text = ''
        self.current_contact = None
        self.save_button.text = 'Save Contact'
        self.status_label.text = ''
    
    def search_contacts(self, instance):
        query = self.name_input.text.strip()
        if query:
            self.refresh_contacts_list(search_query=query)
            self.status_label.text = f'Search results for: "{query}"'
            self.status_label.color = get_color_from_hex('#3498db')  # Blue
        else:
            self.refresh_contacts_list()
            self.status_label.text = 'Showing all contacts'
            self.status_label.color = get_color_from_hex('#3498db')
    
    def show_error_popup(self, message):
        content = BoxLayout(orientation='vertical', padding=10)
        error_label = StylishLabel(
            text=message,
            color=get_color_from_hex('#e74c3c')
        )
        
        dismiss_button = RoundedButton(
            text='OK',
            size_hint_y=None,
            height=40,
            background_color=get_color_from_hex('#3498db')
        )
        
        content.add_widget(error_label)
        content.add_widget(dismiss_button)
        
        popup = Popup(
            title='Error',
            content=content,
            size_hint=(0.8, 0.3),
            auto_dismiss=False
        )
        
        dismiss_button.bind(on_press=popup.dismiss)
        popup.open()


if __name__ == '__main__':
    PhoneBookApp().run()