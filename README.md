# Customer-Support-Ticketing-System
import code
import datetime

class Ticket:
    def _init_(self, ticket_id, user, issue, status='Open'):
        self.ticket_id = ticket_id
        self.user = user
        self.issue = issue
        self.status = status
        self.creation_date = datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S")
        self.updates = []

    def update_status(self, status, comment):
        self.status = status
        self.updates.append((datetime.datetime.now().strftime("%Y-%m-%d %H:%M:%S"), comment))

    def to_dict(self):
        return {
            'ticket_id': self.ticket_id,
            'user': self.user,
            'issue': self.issue,
            'status': self.status,
            'creation_date': self.creation_date,
            'updates': self.updates
        }

class User:
    def _init_(self, name):
        self.name = name

    def to_dict(self):
        return {'name': self.name}

class SupportSystem:
    def _init_(self):
        self.tickets = []
        self.users = []
        self.ticket_counter = 1

    def add_user(self, name):
        user = User(name)
        self.users.append(user)
        print(f"User '{name}' added.")

    def create_ticket(self, user_name, issue):
        user = self._find_user(user_name)
        if not user:
            print(f"User '{user_name}' not found.")
            return

        ticket = Ticket(self.ticket_counter, user_name, issue)
        self.tickets.append(ticket)
        self.ticket_counter += 1
        print(f"Ticket '{ticket.ticket_id}' created for user '{user_name}'.")

    def update_ticket(self, ticket_id, status, comment):
        ticket = self._find_ticket(ticket_id)
        if not ticket:
            print(f"Ticket '{ticket_id}' not found.")
            return

        ticket.update_status(status, comment)
        print(f"Ticket '{ticket_id}' updated.")

    def view_tickets(self):
        if not self.tickets:
            print("No tickets in the system.")
            return
        for ticket in self.tickets:
            print(f"Ticket ID: {ticket.ticket_id}, User: {ticket.user}, Issue: {ticket.issue}, Status: {ticket.status}, Created: {ticket.creation_date}")
            for update in ticket.updates:
                print(f"  Updated: {update[0]}, Comment: {update[1]}")

    def _find_user(self, name):
        for user in self.users:
            if user.name == name:
                return user
        return None

    def _find_ticket(self, ticket_id):
        for ticket in self.tickets:
            if ticket.ticket_id == ticket_id:
                return ticket
        return None

    def save_to_file(self, filename):
        data = {
            'tickets': [ticket.to_dict() for ticket in self.tickets],
            'users': [user.to_dict() for user in self.users],
            'ticket_counter': self.ticket_counter
        }
        with open(filename, 'w') as file:
            code.dump(data, file)
        print(f"System data saved to {filename}.")

    def load_from_file(self, filename):
        try:
            with open(filename, 'r') as file:
                data = code.load(file)
                self.tickets = [Ticket(**ticket_data) for ticket_data in data['tickets']]
                self.users = [User(**user_data) for user_data in data['users']]
                self.ticket_counter = data['ticket_counter']
            print(f"System data loaded from {filename}.")
        except FileNotFoundError:
            print(f"File {filename} not found.")
