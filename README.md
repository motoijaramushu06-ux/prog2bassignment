# prog2bassignment
assignment
CREATE DATABASE RaceeDay;

USE RaceeDay;

CREATE TABLE Organiser (
    organiser_id INT IDENTITY(1,1) PRIMARY KEY,
    full_name NVARCHAR(100) NOT NULL,
    email NVARCHAR(100) NOT NULL UNIQUE,
    phone NVARCHAR(20) NOT NULL,
    organisation NVARCHAR(100) NOT NULL,
    password_hash NVARCHAR(255) NOT NULL,
    profile_pic_url NVARCHAR(500) NULL,
    is_verified BIT DEFAULT 0,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE()
);
CREATE TABLE [User] (
    user_id INT IDENTITY(1,1) PRIMARY KEY,
    full_name NVARCHAR(100) NOT NULL,
    email NVARCHAR(100) NOT NULL UNIQUE,
    phone NVARCHAR(20) NOT NULL,
    date_of_birth DATE NOT NULL,
    id_number NVARCHAR(13) NOT NULL UNIQUE,
    emergency_contact NVARCHAR(100) NOT NULL,
    emergency_phone NVARCHAR(20) NOT NULL,
    password_hash NVARCHAR(255) NOT NULL,
    profile_pic_url NVARCHAR(500) NULL,
    preferred_language NVARCHAR(20) DEFAULT 'English',
    is_active BIT DEFAULT 1,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE()
);
CREATE TABLE Category (
    category_id INT IDENTITY(1,1) PRIMARY KEY,
    category_name NVARCHAR(50) NOT NULL,
    category_description NVARCHAR(255) NULL,
    distance_km DECIMAL(5,2) NOT NULL,
    entry_fee DECIMAL(10,2) NOT NULL,
    age_min INT NULL,
    age_max INT NULL,
    gender_allowed NVARCHAR(10) DEFAULT 'ALL' 
        CHECK (gender_allowed IN ('ALL', 'MALE', 'FEMALE')),
    is_active BIT DEFAULT 1,
    created_at DATETIME DEFAULT GETDATE()
);
CREATE TABLE Event (
    event_id INT IDENTITY(1,1) PRIMARY KEY,
    organiser_id INT NOT NULL,
    event_name NVARCHAR(150) NOT NULL,
    event_date DATE NOT NULL,
    event_time TIME NOT NULL,
    venue NVARCHAR(200) NOT NULL,
    city NVARCHAR(100) NOT NULL,
    province NVARCHAR(50) NOT NULL,
    country NVARCHAR(50) DEFAULT 'South Africa',
    max_participants INT NOT NULL CHECK (max_participants > 0),
    event_status NVARCHAR(20) DEFAULT 'OPEN' 
        CHECK (event_status IN ('DRAFT', 'OPEN', 'CLOSED', 'CANCELLED', 'COMPLETED')),
    description NVARCHAR(MAX) NULL,
    route_map_url NVARCHAR(500) NULL,
    weather_provider_ref NVARCHAR(100) NULL,
    terms_and_conditions NVARCHAR(MAX) NULL,
    banner_image_url NVARCHAR(500) NULL,
    is_featured BIT DEFAULT 0,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE(),
    FOREIGN KEY (organiser_id) REFERENCES Organiser(organiser_id) ON DELETE CASCADE
);
CREATE TABLE EventCategory (
    event_category_id INT IDENTITY(1,1) PRIMARY KEY,
    event_id INT NOT NULL,
    category_id INT NOT NULL,
    capacity INT NOT NULL CHECK (capacity > 0),
    entries_count INT DEFAULT 0,
    start_time TIME NOT NULL,
    cut_off_time TIME NULL,
    price_override DECIMAL(10,2) NULL,
    is_active BIT DEFAULT 1,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE(),
    FOREIGN KEY (event_id) REFERENCES Event(event_id) ON DELETE CASCADE,
    FOREIGN KEY (category_id) REFERENCES Category(category_id) ON DELETE CASCADE,
    CONSTRAINT UQ_EventCategory UNIQUE (event_id, category_id)
);
CREATE TABLE ParticipantEntry (
    entry_id INT IDENTITY(1,1) PRIMARY KEY,
    event_category_id INT NOT NULL,
    user_id INT NOT NULL,
    entry_date DATETIME DEFAULT GETDATE(),
    race_number INT NOT NULL UNIQUE,
    t_shirt_size NVARCHAR(10) NULL 
        CHECK (t_shirt_size IN ('XS', 'S', 'M', 'L', 'XL', 'XXL', 'XXXL')),
    emergency_contact NVARCHAR(100) NOT NULL,
    emergency_phone NVARCHAR(20) NOT NULL,
    payment_status NVARCHAR(20) DEFAULT 'PENDING' 
        CHECK (payment_status IN ('PENDING', 'PAID', 'FAILED', 'REFUNDED')),
    payment_reference NVARCHAR(100) NULL,
    entry_status NVARCHAR(20) DEFAULT 'CONFIRMED' 
        CHECK (entry_status IN ('PENDING', 'CONFIRMED', 'WAITLIST', 'CANCELLED', 'WITHDRAWN')),
    result_time_seconds INT NULL,
    result_time_formatted AS 
        CASE 
            WHEN result_time_seconds IS NOT NULL 
            THEN CONVERT(VARCHAR(8), DATEADD(SECOND, result_time_seconds, 0), 108)
            ELSE NULL 
        END PERSISTED,
    result_position INT NULL,
    result_status NVARCHAR(10) DEFAULT 'DNS' 
        CHECK (result_status IN ('DNS', 'DNF', 'FINISHED')),
    result_certificate_url NVARCHAR(500) NULL,
    wave_start_time TIME NULL,
    notes NVARCHAR(500) NULL,
    created_at DATETIME DEFAULT GETDATE(),
    updated_at DATETIME DEFAULT GETDATE(),
    FOREIGN KEY (event_category_id) REFERENCES EventCategory(event_category_id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES [User](user_id) ON DELETE CASCADE,
    CONSTRAINT UQ_UserEventCategory UNIQUE (user_id, event_category_id)
);





INSERT INTO Organiser (full_name, email, phone, organisation, password_hash, is_verified) VALUES
('Thabo Mokoena', 'thabo@comrades.co.za', '0821234567', 'Comrades Marathon Association', 'hash_12345_secure', 1),
('Lindiwe Nkosi', 'lindiwe@capetowncycletour.com', '0839876543', 'Cape Town Cycle Tour Trust', 'hash_67890_secure', 1),
('Sipho Zulu', 'sipho@sowetomarathon.co.za', '0791122334', 'Soweto Marathon Organisers', 'hash_abcde_secure', 1),
('Sarah van der Merwe', 'sarah@twinoceans.co.za', '0829988776', 'Two Oceans Marathon NPC', 'hash_fghij_secure', 1);

INSERT INTO [User] (full_name, email, phone, date_of_birth, id_number, emergency_contact, emergency_phone, password_hash, preferred_language) VALUES
('Zanele Dlamini', 'zanele.dlamini@gmail.com', '0723344556', '1990-05-12', '9005121234088', 'Sibusiso Dlamini', '0825566778', 'hash_user_001', 'English'),
('Michael Jacobs', 'michael.jacobs@yahoo.com', '0834455667', '1985-09-23', '8509231234089', 'Sarah Jacobs', '0846677889', 'hash_user_002', 'English'),
('Priya Naidoo', 'priya.naidoo@outlook.com', '0715566778', '1995-11-30', '9511301234090', 'Raj Naidoo', '0737788990', 'hash_user_003', 'English'),
('Johan Botha', 'johan.botha@telkomsa.net', '0826677889', '1988-03-15', '8803151234091', 'Marietjie Botha', '0838899001', 'hash_user_004', 'Afrikaans'),
('Nomsa Mthembu', 'nomsa.mthembu@gmail.com', '0737788991', '1992-07-22', '9207221234092', 'Thabo Mthembu', '0749900112', 'hash_user_005', 'isiZulu');

INSERT INTO Category (category_name, category_description, distance_km, entry_fee, age_min, age_max, gender_allowed) VALUES
('5km Fun Run', 'Perfect for families and beginners', 5.0, 50.00, 6, 99, 'ALL'),
('10km Road Race', 'Great for intermediate runners', 10.0, 100.00, 12, 99, 'ALL'),
('21.1km Half Marathon', 'Challenge yourself with the classic half marathon distance', 21.1, 200.00, 16, 99, 'ALL'),
('42.2km Marathon', 'The ultimate road running challenge', 42.2, 350.00, 18, 99, 'ALL'),
('42.2km Marathon - Women Only', 'Women-only marathon category', 42.2, 350.00, 18, 99, 'FEMALE'),
('56km Ultra Marathon', 'For the true ultra-distance athletes', 56.0, 450.00, 20, 99, 'ALL'),
('109km Comrades Ultra', 'The Ultimate Human Race - 109km from PMB to Durban', 109.0, 550.00, 20, 99, 'ALL');

INSERT INTO Event (organiser_id, event_name, event_date, event_time, venue, city, province, max_participants, event_status, description, route_map_url, is_featured) VALUES
(1, 'Comrades Marathon 2026', '2026-06-15', '05:30:00', 'Pietermaritzburg City Hall', 'Pietermaritzburg', 'KZN', 25000, 'OPEN', 'The Ultimate Human Race - 109km from Pietermaritzburg to Durban. One of the oldest and most prestigious ultra-marathons in the world.', 'https://maps.example.com/comrades-2026', 1),
(2, 'Cape Town Cycle Tour 2026', '2026-03-08', '06:00:00', 'Grand Parade', 'Cape Town', 'WC', 35000, 'OPEN', 'The worlds largest timed cycle race. A 109km scenic route around the Cape Peninsula.', 'https://maps.example.com/ctct-2026', 1),
(3, 'Soweto Marathon 2026', '2026-11-07', '06:00:00', 'FNB Stadium', 'Johannesburg', 'GP', 20000, 'OPEN', 'Running through the heart of Soweto. Experience the spirit and energy of this iconic township marathon.', 'https://maps.example.com/soweto-2026', 0),
(4, 'Two Oceans Marathon 2026', '2026-04-10', '05:45:00', 'UCT Rugby Fields', 'Cape Town', 'WC', 16000, 'OPEN', 'The worlds most beautiful marathon. A 56km ultra along the Cape Peninsula with stunning ocean views.', 'https://maps.example.com/two-oceans-2026', 1);

INSERT INTO EventCategory (event_id, category_id, capacity, entries_count, start_time, cut_off_time, price_override) VALUES
(1, 7, 12000, 0, '05:30:00', '11:30:00', NULL),
(1, 6, 8000, 0, '05:30:00', '11:30:00', 400.00),
(1, 4, 5000, 0, '05:30:00', '11:30:00', 320.00),
(2, 1, 5000, 0, '06:00:00', '09:00:00', 45.00),
(2, 2, 10000, 0, '06:15:00', '10:15:00', 90.00),
(2, 3, 20000, 0, '06:30:00', '12:00:00', 180.00),
(3, 3, 8000, 0, '06:00:00', '10:30:00', NULL),
(3, 4, 12000, 0, '06:00:00', '12:00:00', NULL),
(4, 6, 8000, 0, '05:45:00', '11:45:00', NULL),
(4, 4, 5000, 0, '05:45:00', '11:45:00', 330.00),
(4, 3, 3000, 0, '05:45:00', '10:45:00', 190.00);

INSERT INTO ParticipantEntry (event_category_id, user_id, race_number, t_shirt_size, emergency_contact, emergency_phone, payment_status, entry_status, result_status) VALUES
(1, 1, 1001, 'M', 'Sibusiso Dlamini', '0825566778', 'PAID', 'CONFIRMED', 'DNS'),
(6, 2, 1002, 'L', 'Sarah Jacobs', '0846677889', 'PAID', 'CONFIRMED', 'DNS'),
(7, 3, 1003, 'S', 'Raj Naidoo', '0737788990', 'PAID', 'CONFIRMED', 'DNS'),
(9, 1, 1004, 'M', 'Sibusiso Dlamini', '0825566778', 'PENDING', 'PENDING', 'DNS'),
(8, 4, 1005, 'XL', 'Marietjie Botha', '0838899001', 'PAID', 'CONFIRMED', 'DNS'),
(11, 5, 1006, 'S', 'Thabo Mthembu', '0749900112', 'PAID', 'CONFIRMED', 'DNS'),
(9, 2, 1007, 'L', 'Sarah Jacobs', '0846677889', 'PAID', 'CONFIRMED', 'DNS')

UPDATE ParticipantEntry SET 
    result_time_seconds = 39200,
    result_position = 125,
    result_status = 'FINISHED'
WHERE entry_id = 1;

UPDATE ParticipantEntry SET 
    result_time_seconds = 7200,
    result_position = 450,
    result_status = 'FINISHED'
WHERE entry_id = 2;

UPDATE ParticipantEntry SET 
    result_time_seconds = 9000,
    result_position = 320,
    result_status = 'FINISHED'
WHERE entry_id = 3;


SELECT
    e.event_id,
    e.event_name,
    e.event_date,
    e.city,
    e.province,
    e.event_status,
    o.full_name AS organiser_name,
    o.organisation
FROM Event e
JOIN Organiser o ON e.organiser_id = o.organiser_id;

SELECT
    e.event_name,
    c.category_name,
    c.distance_km,
    ec.capacity,
    ec.entries_count,
    (ec.capacity - ec.entries_count) AS slots_remaining,
    COALESCE(ec.price_override, c.entry_fee) AS current_price
FROM EventCategory ec
JOIN Event e ON ec.event_id = e.event_id
JOIN Category c ON ec.category_id = c.category_id;

SELECT
    u.full_name,
    u.email,
    pe.race_number,
    c.category_name,
    pe.payment_status,
    pe.entry_status
FROM ParticipantEntry pe
JOIN [User] u ON pe.user_id = u.user_id
JOIN EventCategory ec ON pe.event_category_id = ec.event_category_id
JOIN Event e ON ec.event_id = e.event_id
JOIN Category c ON ec.category_id = c.category_id
WHERE e.event_name = 'Comrades Marathon 2026';

SELECT
    e.event_name,
    c.category_name,
    u.full_name,
    pe.race_number,
    pe.result_time_formatted AS finish_time,
    pe.result_position,
    pe.result_status
FROM ParticipantEntry pe
JOIN [User] u ON pe.user_id = u.user_id
JOIN EventCategory ec ON pe.event_category_id = ec.event_category_id
JOIN Event e ON ec.event_id = e.event_id
JOIN Category c ON ec.category_id = c.category_id
WHERE pe.result_status = 'FINISHED'
ORDER BY e.event_name, pe.result_position;

SELECT
    e.event_name,
    COUNT(pe.entry_id) AS total_entries,
    SUM(CASE WHEN pe.payment_status = 'PAID' THEN 1 ELSE 0 END) AS paid_entries
FROM Event e
LEFT JOIN EventCategory ec ON e.event_id = ec.event_id
LEFT JOIN ParticipantEntry pe ON ec.event_category_id = pe.event_category_id
GROUP BY e.event_name;

SELECT
    e.event_name,
    e.event_date,
    c.category_name,
    pe.race_number,
    pe.entry_status,
    pe.result_time_formatted,
    pe.result_position
FROM ParticipantEntry pe
JOIN EventCategory ec ON pe.event_category_id = ec.event_category_id
JOIN Event e ON ec.event_id = e.event_id
JOIN Category c ON ec.category_id = c.category_id
WHERE pe.user_id = 1;

UPDATE EventCategory
SET entries_count = (SELECT COUNT(*) FROM ParticipantEntry WHERE event_category_id = EventCategory.event_category_id)
WHERE event_category_id IN (SELECT event_category_id FROM ParticipantEntry);

MY YOUTUBE LINK >> https://youtu.be/utMiRTqIP7E?si=I1hSBesaCZ1SGAFI





