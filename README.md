-- Create database
CREATE DATABASE EventHackathonSearch;
USE EventHackathonSearch;

-- Create roles table
CREATE TABLE IF NOT EXISTS roles (
    id INT AUTO_INCREMENT PRIMARY KEY,
    role_name VARCHAR(255) NOT NULL UNIQUE,
    role_description TEXT
);

-- Insert initial roles
INSERT INTO roles (role_name, role_description) VALUES 
('admin', 'Administrator with full access'),
('student', 'User with limited access to events'),
('organizer', 'Event organizer with approval access and college-limited event management');

-- Create users table
CREATE TABLE IF NOT EXISTS users (
    id INT AUTO_INCREMENT PRIMARY KEY,
    username VARCHAR(255) NOT NULL UNIQUE,
    password VARCHAR(255) NOT NULL,
    role ENUM('admin', 'student', 'organizer') NOT NULL DEFAULT 'student',
    college VARCHAR(255),
    email VARCHAR(255) UNIQUE,
    date_registered TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    approval_status ENUM('pending', 'approved') DEFAULT 'approved'
);

-- Insert admin user with no college information
INSERT INTO users (username, password, role, college, email, approval_status) VALUES 
('gokulk', 'gokulk', 'admin', NULL, 'admin@college.com', 'approved');

-- Insert sample student and organizer users
INSERT INTO users (username, password, role, college, email, approval_status) VALUES 
('student_user', 'hashedpassword123', 'student', 'Rajalakshmi Engineering College', 'student@college.com', 'approved'),
('organizer_user', 'hashedpassword123', 'organizer', 'Rajalakshmi Engineering College', 'organizer@college.com', 'pending'); -- organizer waiting for admin approval

-- Create events table
CREATE TABLE IF NOT EXISTS events (
    id INT AUTO_INCREMENT PRIMARY KEY,
    event_name VARCHAR(255) NOT NULL,
    event_description TEXT,
    event_date DATE,
    location VARCHAR(255),
    college VARCHAR(255),
    created_by INT NOT NULL,
    event_poster VARCHAR(255) DEFAULT 'resources/images/default_poster.jpg',
    date_created TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (created_by) REFERENCES users(id) ON DELETE CASCADE
);

-- Sample events created by admin user
INSERT INTO events (event_name, event_description, event_date, location, college, created_by) VALUES 
('Tech Talk', 'A talk about the latest trends in technology', '2024-11-15', 'Rajalakshmi Auditorium', 'Rajalakshmi Engineering College', 1),
('Hackathon', 'A 24-hour coding competition', '2024-12-01', 'Rajalakshmi Hackerspace', 'Rajalakshmi Engineering College', 1);

-- Create registrations table
CREATE TABLE IF NOT EXISTS registrations (
    id INT AUTO_INCREMENT PRIMARY KEY,
    user_id INT NOT NULL,
    event_id INT NOT NULL,
    registration_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    approval_status ENUM('pending', 'approved', 'rejected') DEFAULT 'pending',
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE CASCADE,
    UNIQUE(user_id, event_id)
);

-- Sample registrations
INSERT INTO registrations (user_id, event_id, approval_status) VALUES 
(2, 1, 'pending'), -- student registration for event, awaiting organizer approval
(2, 2, 'pending');
and # Database Configuration
db.url=jdbc:mysql://localhost:3306/EventHackathonSearch
db.username=root
db.password=your_password

# File Paths
event.defaultPosterPath=resources/images/default_poster.jpg
