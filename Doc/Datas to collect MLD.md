-- ============================================

-- SECTION: GESTION DES UTILISATEURS

-- ============================================

-- Table de base pour tous les utilisateurs (données communes de sécurité)

CREATE TABLE base_users (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    email VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NOT NULL,
    status ENUM('active', 'inactive', 'suspended') DEFAULT 'active',
    last_login_at TIMESTAMP NULL,
    email_verified_at TIMESTAMP NULL,
    failed_login_attempts TINYINT DEFAULT 0,
    locked_until TIMESTAMP NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    created_by BIGINT NULL,
    INDEX idx_base_users_email (email),
    INDEX idx_base_users_status (status),
    INDEX idx_base_users_deleted (deleted_at)

);
-- Table des administrateurs

CREATE TABLE admins (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    base_user_id BIGINT UNIQUE NOT NULL,
    firstname VARCHAR(100) NOT NULL,
    lastname VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    permissions_level ENUM('super_admin', 'admin', 'limited_admin') DEFAULT 'admin'
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (base_user_id) REFERENCES base_users(id) ON DELETE CASCADE,
    INDEX idx_admins_permissions (permissions_level)

);
-- Table des techniciens externes

CREATE TABLE external_technicians (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    base_user_id BIGINT UNIQUE NOT NULL,
    firstname VARCHAR(100) NOT NULL,
    lastname VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    location VARCHAR(255),
    disponibilities JSON,
    partner_id BIGINT,
    hourly_rate DECIMAL(8,2),
    specializations JSON,
    certification_level VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (base_user_id) REFERENCES base_users(id) ON DELETE CASCADE,
    INDEX idx_external_tech_partner (partner_id),
    INDEX idx_external_tech_location (location)

);
-- Table des techniciens internes

CREATE TABLE internal_technicians (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    base_user_id BIGINT UNIQUE NOT NULL,
    firstname VARCHAR(100) NOT NULL,
    lastname VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    location VARCHAR(255),
    disponibilities JSON,
    employee_id VARCHAR(50) UNIQUE,
    department VARCHAR(100),
    manager_id BIGINT,
    salary_grade VARCHAR(20),
    specializations JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (base_user_id) REFERENCES base_users(id) ON DELETE CASCADE,
    FOREIGN KEY (manager_id) REFERENCES internal_technicians(id),
    INDEX idx_internal_tech_employee (employee_id),
    INDEX idx_internal_tech_manager (manager_id)

);
-- Table des chefs de projet

CREATE TABLE chef_projets (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    base_user_id BIGINT UNIQUE NOT NULL,
    firstname VARCHAR(100) NOT NULL,
    lastname VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    location VARCHAR(255),
    disponibilities JSON,
    employee_id VARCHAR(50) UNIQUE,
    department VARCHAR(100),
    max_concurrent_projects TINYINT DEFAULT 5,
    certification_level VARCHAR(50),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (base_user_id) REFERENCES base_users(id) ON DELETE CASCADE,
    INDEX idx_chef_projets_employee (employee_id)

);
-- Table du support

CREATE TABLE support_users (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    base_user_id BIGINT UNIQUE NOT NULL,
    firstname VARCHAR(100) NOT NULL,
    lastname VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    support_level ENUM('level1', 'level2', 'level3') DEFAULT 'level1',
    languages_spoken JSON,
    shift_schedule JSON,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (base_user_id) REFERENCES base_users(id) ON DELETE CASCADE,
    INDEX idx_support_level (support_level)

);
-- Table ADV (Administration des Ventes)

CREATE TABLE adv_users (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    base_user_id BIGINT UNIQUE NOT NULL,
    firstname VARCHAR(100) NOT NULL,
    lastname VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    employee_id VARCHAR(50) UNIQUE,
    department VARCHAR(100),
    processing_capacity TINYINT DEFAULT 10,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (base_user_id) REFERENCES base_users(id) ON DELETE CASCADE,
    INDEX idx_adv_employee (employee_id)

);
-- Table des commerciaux

CREATE TABLE commercials (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    base_user_id BIGINT UNIQUE NOT NULL,
    firstname VARCHAR(100) NOT NULL,
    lastname VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    employee_id VARCHAR(50) UNIQUE,
    territory VARCHAR(255),
    commission_rate DECIMAL(5,2) DEFAULT 0.00,
    sales_target_monthly DECIMAL(12,2),
    manager_id BIGINT,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (base_user_id) REFERENCES base_users(id) ON DELETE CASCADE,
    FOREIGN KEY (manager_id) REFERENCES commercials(id),
    INDEX idx_commercials_employee (employee_id),
    INDEX idx_commercials_manager (manager_id)

);

-- Table de la comptabilité

CREATE TABLE compta_users (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    base_user_id BIGINT UNIQUE NOT NULL,
    firstname VARCHAR(100) NOT NULL,
    lastname VARCHAR(100) NOT NULL,
    phone VARCHAR(20),
    employee_id VARCHAR(50) UNIQUE,
    access_level ENUM('junior', 'senior', 'manager') DEFAULT 'junior',
    signature_limit DECIMAL(12,2) DEFAULT 0.00,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (base_user_id) REFERENCES base_users(id) ON DELETE CASCADE,
    INDEX idx_compta_employee (employee_id),
    INDEX idx_compta_access (access_level)

);

-- Table pour les rôles supplémentaires (solution pour rôles multiples)

CREATE TABLE user_additional_roles (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    base_user_id BIGINT NOT NULL,
    role_type ENUM('admin', 'external_technician', 'internal_technician', 'chef_projet', 'support', 'adv', 'commercial', 'compta') NOT NULL,
    role_id BIGINT NOT NULL,
    is_primary BOOLEAN DEFAULT FALSE,
    assigned_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    assigned_by BIGINT,
    expires_at TIMESTAMP NULL,
    FOREIGN KEY (base_user_id) REFERENCES base_users(id) ON DELETE CASCADE,
    INDEX idx_user_roles (base_user_id, role_type),
    INDEX idx_user_roles_primary (base_user_id, is_primary)

);
-- ============================================
-- SECTION: CLIENTS & PARTENAIRES
-- ============================================
CREATE TABLE clients (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    company_name VARCHAR(255) NOT NULL,
    contact_firstname VARCHAR(100),
    contact_lastname VARCHAR(100),
    email VARCHAR(255),
    phone VARCHAR(20),
    address TEXT,
    city VARCHAR(100),
    postal_code VARCHAR(20),
    country VARCHAR(100) DEFAULT 'France',
    siret VARCHAR(14),
    vat_number VARCHAR(20),
    status ENUM('active', 'inactive', 'prospect', 'suspended') DEFAULT 'prospect',
    assigned_commercial_id BIGINT,
    credit_limit DECIMAL(12,2) DEFAULT 0.00,
    payment_terms TINYINT DEFAULT 30,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (assigned_commercial_id) REFERENCES commercials(id),
    INDEX idx_clients_status (status),
    INDEX idx_clients_commercial (assigned_commercial_id),
    INDEX idx_clients_company (company_name)

);
CREATE TABLE partners (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    partner_name VARCHAR(255) NOT NULL,
    contact_firstname VARCHAR(100),
    contact_lastname VARCHAR(100),
    email VARCHAR(255),
    phone VARCHAR(20),
    address TEXT,
    city VARCHAR(100),
    postal_code VARCHAR(20),
    country VARCHAR(100) DEFAULT 'France',
    siret VARCHAR(14),
    vat_number VARCHAR(20),
    partner_type ENUM('reseller', 'installer', 'maintenance', 'hybrid') DEFAULT 'reseller',
    commission_rate DECIMAL(5,2) DEFAULT 0.00,
    status ENUM('active', 'inactive', 'suspended', 'pending_approval') DEFAULT 'pending_approval',
    contract_start_date DATE,
    contract_end_date DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    deleted_at TIMESTAMP NULL,
    INDEX idx_partners_status (status),
    INDEX idx_partners_type (partner_type)

);
-- ============================================
-- SECTION: TICKETS & SAV
-- ============================================
CREATE TABLE tickets (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    title VARCHAR(255) NOT NULL,
    description TEXT,
    domain VARCHAR(100),
    priority ENUM('low', 'medium', 'high', 'critical') DEFAULT 'medium',
    deadline DATETIME NULL,
    status ENUM('open', 'assigned', 'in_progress', 'pending_client', 'resolved', 'closed', 'cancelled') DEFAULT 'open',
    partner_id BIGINT,
    client_id BIGINT NOT NULL,
    assigned_technician_id BIGINT NULL,
    assigned_technician_type ENUM('internal', 'external') NULL,
    created_by_user_id BIGINT NOT NULL,
    created_by_user_type VARCHAR(50) NOT NULL,
    estimated_hours DECIMAL(5,2),
    actual_hours DECIMAL(5,2),
    billable_hours DECIMAL(5,2),
    resolution_notes TEXT,
    client_satisfaction TINYINT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    closed_at TIMESTAMP NULL,
    deleted_at TIMESTAMP NULL,
    FOREIGN KEY (partner_id) REFERENCES partners(id),
    FOREIGN KEY (client_id) REFERENCES clients(id),
    INDEX idx_tickets_status (status),
    INDEX idx_tickets_priority (priority),
    INDEX idx_tickets_client (client_id),
    INDEX idx_tickets_assigned (assigned_technician_id, assigned_technician_type),
    INDEX idx_tickets_created_at (created_at)

);
CREATE TABLE ticket_history (
    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    ticket_id BIGINT NOT NULL,
    user_id BIGINT NOT NULL,
    user_type VARCHAR(50) NOT NULL,
    action VARCHAR(100) NOT NULL,
    old_values JSON,
    new_values JSON,
    comment TEXT,
    is_internal BOOLEAN DEFAULT TRUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    FOREIGN KEY (ticket_id) REFERENCES tickets(id) ON DELETE CASCADE,
    INDEX idx_ticket_history_ticket (ticket_id),
    INDEX idx_ticket_history_user (user_id, user_type)

);
-- ============================================
-- SECTION: SÉCURITÉ & AUDIT
-- ============================================
CREATE TABLE audit_logs (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    table_name VARCHAR(100) NOT NULL,
    record_id BIGINT NOT NULL,
    action ENUM('INSERT', 'UPDATE', 'DELETE', 'SELECT') NOT NULL,
    old_values JSON,
    new_values JSON,
    user_id BIGINT,
    user_type VARCHAR(50),
    ip_address VARCHAR(45),
    user_agent TEXT,
    session_id VARCHAR(128),
    request_id VARCHAR(36),
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_audit_logs_table (table_name, record_id),
    INDEX idx_audit_logs_user (user_id, user_type),
    INDEX idx_audit_logs_action (action),
    INDEX idx_audit_logs_date (created_at)

);

CREATE TABLE user_sessions (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    user_type VARCHAR(50) NOT NULL,
    session_token VARCHAR(128) UNIQUE NOT NULL,
    refresh_token VARCHAR(128) UNIQUE,
    ip_address VARCHAR(45) NOT NULL,
    user_agent TEXT,
    device_fingerprint VARCHAR(64),
    is_active BOOLEAN DEFAULT TRUE,
    last_activity TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    expires_at TIMESTAMP NOT NULL,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    INDEX idx_user_sessions_token (session_token),
    INDEX idx_user_sessions_user (user_id, user_type),
    INDEX idx_user_sessions_active (is_active, expires_at)

);

-- ============================================  
-- SECTION: TABLES UTILITAIRES
-- ============================================  
CREATE TABLE disponibilites (

    id BIGINT PRIMARY KEY AUTO_INCREMENT,
    user_id BIGINT NOT NULL,
    user_type VARCHAR(50) NOT NULL,
    day_of_week TINYINT NOT NULL CHECK (day_of_week >= 1 AND day_of_week <= 7),
    start_time TIME NOT NULL,
    end_time TIME NOT NULL,
    is_available BOOLEAN DEFAULT TRUE,
    break_start_time TIME,
    break_end_time TIME,
    max_interventions TINYINT DEFAULT 1,
    travel_radius_km SMALLINT DEFAULT 50,
    hourly_rate DECIMAL(8,2),
    overtime_rate DECIMAL(8,2),
    effective_from DATE NOT NULL,
    effective_to DATE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_disponibilites_user (user_id, user_type),
    INDEX idx_disponibilites_day (day_of_week),
    INDEX idx_disponibilites_effective (effective_from, effective_to)

);

-- ============================================  
-- SECTION: PLANNING & INTERVENTION  
-- ============================================  
  
CREATE TABLE interventions (  

id BIGINT PRIMARY KEY AUTO_INCREMENT,
ticket_id BIGINT NOT NULL,  
intervention_date DATE NOT NULL,  
start_datetime DATETIME,  
end_datetime DATETIME,  
external_technician_id BIGINT,  
chefprojet_id BIGINT,  
status ENUM('scheduled', 'in_progress', 'completed', 'cancelled', 'postponed') DEFAULT 'scheduled',  
type_intervention VARCHAR(100),  
location VARCHAR(255),  
notes TEXT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_interventions_ticket (ticket_id),  
INDEX idx_interventions_date (intervention_date),  
INDEX idx_interventions_external_tech (external_technician_id),  
INDEX idx_interventions_chef (chefprojet_id),  
INDEX idx_interventions_status (status)  

);  
  
CREATE TABLE documentation (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
intervention_id BIGINT,  
title VARCHAR(255) NOT NULL,  
description TEXT,  
photo VARCHAR(500),  
contrainte TEXT,  
technician_note TEXT,  
chef_projet_note TEXT,  
external_technician_id BIGINT,  
chef_projet_id BIGINT,  
intervention_date DATE,  
intervention_starting_date DATETIME,  
intervention_ending_date DATETIME,  
document_type ENUM('photo', 'report', 'checklist', 'other') DEFAULT 'report',  
file_path VARCHAR(500),  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_documentation_intervention (intervention_id),  
INDEX idx_documentation_external_tech (external_technician_id),  
INDEX idx_documentation_chef (chef_projet_id),  
INDEX idx_documentation_date (intervention_date)  
);

-- ============================================  
-- SECTION: GESTION ADV & COMMANDES  
-- ============================================  
  
CREATE TABLE leads (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
company_name VARCHAR(255) NOT NULL,  
contact_person VARCHAR(200),  
email VARCHAR(255),  
phone VARCHAR(20),  
source VARCHAR(100),  
status ENUM('new', 'contacted', 'qualified', 'converted', 'lost') DEFAULT 'new',  
assigned_comercial_id BIGINT,  
qualification_score TINYINT DEFAULT 0,  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
date_last_contact TIMESTAMP NULL,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_leads_status (status),  
INDEX idx_leads_commercial (assigned_comercial_id),  
INDEX idx_leads_source (source)  
);  
  
CREATE TABLE opportunities (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
lead_id BIGINT,  
client_id BIGINT,  
title VARCHAR(255) NOT NULL,  
description TEXT,  
estimated_values DECIMAL(12,2),  
probability TINYINT DEFAULT 0,  
stage ENUM('prospecting', 'qualification', 'proposal', 'negotiation', 'closing', 'won', 'lost') DEFAULT 'prospecting',  
expected_close_date DATE,  
assigned_commercial_id BIGINT,  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_opportunities_lead (lead_id),  
INDEX idx_opportunities_client (client_id),  
INDEX idx_opportunities_commercial (assigned_commercial_id),  
INDEX idx_opportunities_stage (stage)  
);  
  
CREATE TABLE quotes (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
opportunity_id BIGINT,  
client_id BIGINT NOT NULL,  
quote_number VARCHAR(50) UNIQUE NOT NULL,  
title VARCHAR(255) NOT NULL,  
description TEXT,  
total_amount DECIMAL(12,2) NOT NULL,  
tax_amount DECIMAL(12,2) DEFAULT 0.00,  
status ENUM('draft', 'sent', 'accepted', 'rejected', 'expired') DEFAULT 'draft',  
validity_date DATE,  
created_by_user_id BIGINT NOT NULL,  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_quotes_client (client_id),  
INDEX idx_quotes_opportunity (opportunity_id),  
INDEX idx_quotes_status (status),  
INDEX idx_quotes_number (quote_number)  
);  
  
CREATE TABLE quote_items (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
quote_id BIGINT NOT NULL,  
product_service_id BIGINT,  
description TEXT,  
quantity DECIMAL(10,2) NOT NULL,  
unit_price DECIMAL(10,2) NOT NULL,  
total_price DECIMAL(12,2) NOT NULL,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_quote_items_quote (quote_id),  
INDEX idx_quote_items_product (product_service_id)  
);  
  
CREATE TABLE products_services (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
name VARCHAR(255) NOT NULL,  
description TEXT,  
category_id BIGINT,  
unit_price DECIMAL(10,2) NOT NULL,  
cost_price DECIMAL(10,2),  
is_active BOOLEAN DEFAULT TRUE,  
sku VARCHAR(100) UNIQUE,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_products_category (category_id),  
INDEX idx_products_sku (sku),  
INDEX idx_products_active (is_active)  
);  
  
CREATE TABLE product_categories (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
name VARCHAR(100) NOT NULL,  
description TEXT,  
parent_category_id BIGINT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_product_categories_parent (parent_category_id)  
);  
  
-- Section: Gestion ADV & Commandes (suite)  
  
CREATE TABLE orders (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
client_id BIGINT NOT NULL,  
quote_id BIGINT,  
order_number VARCHAR(50) UNIQUE NOT NULL,  
status ENUM('pending', 'confirmed', 'in_production', 'shipped', 'delivered', 'cancelled') DEFAULT 'pending',  
order_date DATE NOT NULL,  
delivery_date DATE,  
shipping_address TEXT,  
billing_address TEXT,  
total_amount DECIMAL(12,2) NOT NULL,  
assigned_adv_id BIGINT,  
notes TEXT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_orders_client (client_id),  
INDEX idx_orders_quote (quote_id),  
INDEX idx_orders_status (status),  
INDEX idx_orders_adv (assigned_adv_id),  
INDEX idx_orders_number (order_number)  
);  
  
CREATE TABLE order_items (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
order_id BIGINT NOT NULL,  
product_service_id BIGINT NOT NULL,  
description TEXT,  
quantity DECIMAL(10,2) NOT NULL,  
unit_price DECIMAL(10,2) NOT NULL,  
total_price DECIMAL(12,2) NOT NULL,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_order_items_order (order_id),  
INDEX idx_order_items_product (product_service_id)  
);  
  
CREATE TABLE deliveries (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
order_id BIGINT NOT NULL,  
delivery_date DATE NOT NULL,  
delivery_address TEXT NOT NULL,  
carrier VARCHAR(100),  
tracking_number VARCHAR(100),  
status ENUM('scheduled', 'in_transit', 'delivered', 'failed', 'returned') DEFAULT 'scheduled',  
delivered_by_user_id BIGINT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_deliveries_order (order_id),  
INDEX idx_deliveries_status (status),  
INDEX idx_deliveries_date (delivery_date)  
);  
  
CREATE TABLE invoices (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
order_id BIGINT,  
client_id BIGINT NOT NULL,  
invoice_number VARCHAR(50) UNIQUE NOT NULL,  
invoice_date DATE NOT NULL,  
due_date DATE NOT NULL,  
total_amount DECIMAL(12,2) NOT NULL,  
tax_amount DECIMAL(12,2) DEFAULT 0.00,  
status ENUM('draft', 'sent', 'paid', 'overdue', 'cancelled') DEFAULT 'draft',  
payment_terms TINYINT DEFAULT 30,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_invoices_client (client_id),  
INDEX idx_invoices_order (order_id),  
INDEX idx_invoices_status (status),  
INDEX idx_invoices_number (invoice_number),  
INDEX idx_invoices_due_date (due_date)  
);  
  
CREATE TABLE invoice_items (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
invoice_id BIGINT NOT NULL,  
description TEXT NOT NULL,  
quantity DECIMAL(10,2) NOT NULL,  
unit_price DECIMAL(10,2) NOT NULL,  
tax_rate DECIMAL(5,2) DEFAULT 20.00,  
total_price DECIMAL(12,2) NOT NULL,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_invoice_items_invoice (invoice_id)  
);  
  
CREATE TABLE payments (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
invoice_id BIGINT NOT NULL,  
payment_date DATE NOT NULL,  
amount DECIMAL(12,2) NOT NULL,  
payment_method ENUM('cash', 'check', 'bank_transfer', 'credit_card', 'other') NOT NULL,  
reference VARCHAR(100),  
status ENUM('pending', 'completed', 'failed', 'cancelled') DEFAULT 'pending',  
processed_by_user_id BIGINT NOT NULL,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_payments_invoice (invoice_id),  
INDEX idx_payments_status (status),  
INDEX idx_payments_date (payment_date)  
);

-- ============================================  
-- SECTION: GESTION PROJET & PLANNING  
-- ============================================  
  
CREATE TABLE projects (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
client_id BIGINT NOT NULL,  
name VARCHAR(255) NOT NULL,  
description TEXT,  
start_date DATE,  
end_date DATE,  
status ENUM('planning', 'active', 'on_hold', 'completed', 'cancelled') DEFAULT 'planning',  
budget DECIMAL(12,2),  
assigned_chef_projet_id BIGINT,  
completion_percentage TINYINT DEFAULT 0,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_projects_client (client_id),  
INDEX idx_projects_chef (assigned_chef_projet_id),  
INDEX idx_projects_status (status),  
INDEX idx_projects_dates (start_date, end_date)  
);  
  
CREATE TABLE project_tasks (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
project_id BIGINT NOT NULL,  
title VARCHAR(255) NOT NULL,  
description TEXT,  
assigned_user_id BIGINT,  
start_date DATE,  
due_date DATE,  
status ENUM('not_started', 'in_progress', 'completed', 'on_hold', 'cancelled') DEFAULT 'not_started',  
priority ENUM('low', 'medium', 'high', 'critical') DEFAULT 'medium',  
estimated_hours DECIMAL(6,2),  
actual_hours DECIMAL(6,2) DEFAULT 0.00,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_project_tasks_project (project_id),  
INDEX idx_project_tasks_assigned (assigned_user_id),  
INDEX idx_project_tasks_status (status),  
INDEX idx_project_tasks_dates (start_date, due_date)  
);  
  
CREATE TABLE project_milestones (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
project_id BIGINT NOT NULL,  
title VARCHAR(255) NOT NULL,  
description TEXT,  
due_date DATE NOT NULL,  
status ENUM('pending', 'completed', 'overdue', 'cancelled') DEFAULT 'pending',  
completion_date DATE,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_project_milestones_project (project_id),  
INDEX idx_project_milestones_due_date (due_date),  
INDEX idx_project_milestones_status (status)  
);  
  
CREATE TABLE resource_allocation (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
project_id BIGINT NOT NULL,  
user_id BIGINT NOT NULL,  
allocated_hours DECIMAL(6,2) NOT NULL,  
start_date DATE NOT NULL,  
end_date DATE NOT NULL,  
rate_per_hour DECIMAL(8,2),  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_resource_allocation_project (project_id),  
INDEX idx_resource_allocation_user (user_id),  
INDEX idx_resource_allocation_dates (start_date, end_date)  
);  
  
CREATE TABLE timesheets (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
user_id BIGINT NOT NULL,  
project_id BIGINT,  
task_id BIGINT,  
date DATE NOT NULL,  
hours_worked DECIMAL(4,2) NOT NULL,  
description TEXT,  
is_billable BOOLEAN DEFAULT TRUE,  
hourly_rate DECIMAL(8,2),  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_timesheets_user (user_id),  
INDEX idx_timesheets_project (project_id),  
INDEX idx_timesheets_task (task_id),  
INDEX idx_timesheets_date (date)  
);

-- ============================================  
-- SECTION: GESTION PROJET & PLANNING  
-- ============================================  
  
CREATE TABLE projects (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
client_id BIGINT NOT NULL,  
name VARCHAR(255) NOT NULL,  
description TEXT,  
start_date DATE,  
end_date DATE,  
status ENUM('planning', 'active', 'on_hold', 'completed', 'cancelled') DEFAULT 'planning',  
budget DECIMAL(12,2),  
assigned_chef_projet_id BIGINT,  
completion_percentage TINYINT DEFAULT 0,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_projects_client (client_id),  
INDEX idx_projects_chef (assigned_chef_projet_id),  
INDEX idx_projects_status (status),  
INDEX idx_projects_dates (start_date, end_date)  
);  
  
CREATE TABLE project_tasks (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
project_id BIGINT NOT NULL,  
title VARCHAR(255) NOT NULL,  
description TEXT,  
assigned_user_id BIGINT,  
start_date DATE,  
due_date DATE,  
status ENUM('not_started', 'in_progress', 'completed', 'on_hold', 'cancelled') DEFAULT 'not_started',  
priority ENUM('low', 'medium', 'high', 'critical') DEFAULT 'medium',  
estimated_hours DECIMAL(6,2),  
actual_hours DECIMAL(6,2) DEFAULT 0.00,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_project_tasks_project (project_id),  
INDEX idx_project_tasks_assigned (assigned_user_id),  
INDEX idx_project_tasks_status (status),  
INDEX idx_project_tasks_dates (start_date, due_date)  
);  
  
CREATE TABLE project_milestones (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
project_id BIGINT NOT NULL,  
title VARCHAR(255) NOT NULL,  
description TEXT,  
due_date DATE NOT NULL,  
status ENUM('pending', 'completed', 'overdue', 'cancelled') DEFAULT 'pending',  
completion_date DATE,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_project_milestones_project (project_id),  
INDEX idx_project_milestones_due_date (due_date),  
INDEX idx_project_milestones_status (status)  
);  
  
CREATE TABLE resource_allocation (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
project_id BIGINT NOT NULL,  
user_id BIGINT NOT NULL,  
allocated_hours DECIMAL(6,2) NOT NULL,  
start_date DATE NOT NULL,  
end_date DATE NOT NULL,  
rate_per_hour DECIMAL(8,2),  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_resource_allocation_project (project_id),  
INDEX idx_resource_allocation_user (user_id),  
INDEX idx_resource_allocation_dates (start_date, end_date)  
);  
  
CREATE TABLE timesheets (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
user_id BIGINT NOT NULL,  
project_id BIGINT,  
task_id BIGINT,  
date DATE NOT NULL,  
hours_worked DECIMAL(4,2) NOT NULL,  
description TEXT,  
is_billable BOOLEAN DEFAULT TRUE,  
hourly_rate DECIMAL(8,2),  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_timesheets_user (user_id),  
INDEX idx_timesheets_project (project_id),  
INDEX idx_timesheets_task (task_id),  
INDEX idx_timesheets_date (date)  
);
-- ============================================  
-- SECTION: GESTION CONTRACTUELLE  
-- ============================================  
  
CREATE TABLE contracts (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
client_id BIGINT NOT NULL,  
contract_number VARCHAR(50) UNIQUE NOT NULL,  
title VARCHAR(255) NOT NULL,  
start_date DATE NOT NULL,  
end_date DATE NOT NULL,  
value DECIMAL(12,2) NOT NULL,  
status ENUM('draft', 'active', 'expired', 'terminated') DEFAULT 'draft',  
contract_type VARCHAR(100),  
renewal_terms TEXT,  
assigned_user_id BIGINT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_contracts_client (client_id),  
INDEX idx_contracts_status (status),  
INDEX idx_contracts_dates (start_date, end_date),  
INDEX idx_contracts_number (contract_number)  
);  
  
CREATE TABLE contract_services (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
contract_id BIGINT NOT NULL,  
service_description TEXT NOT NULL,  
sla_terms TEXT,  
billing_frequency ENUM('monthly', 'quarterly', 'annual') DEFAULT 'monthly',  
unit_price DECIMAL(10,2) NOT NULL,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_contract_services_contract (contract_id)  
);  
  
CREATE TABLE sla_metrics (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
contract_id BIGINT NOT NULL,  
metric_name VARCHAR(100) NOT NULL,  
target_value DECIMAL(8,2) NOT NULL,  
measurement_unit VARCHAR(50) NOT NULL,  
penalty_clause TEXT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_sla_metrics_contract (contract_id)  
);  
  
CREATE TABLE maintenance_schedules (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
contract_id BIGINT NOT NULL,  
equipment_id BIGINT,  
frequency ENUM('weekly', 'monthly', 'quarterly', 'annual') NOT NULL,  
next_maintenance_date DATE NOT NULL,  
assigned_technician_id BIGINT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_maintenance_schedules_contract (contract_id),  
INDEX idx_maintenance_schedules_equipment (equipment_id),  
INDEX idx_maintenance_schedules_next_date (next_maintenance_date)  
);  
  
-- ============================================  
-- SECTION: GESTION COMMUNICATIONS & MARKETING  
-- ============================================  
  
CREATE TABLE campaigns (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
name VARCHAR(255) NOT NULL,  
type ENUM('email', 'sms', 'phone', 'direct_mail', 'mixed') NOT NULL,  
start_date DATE,  
end_date DATE,  
budget DECIMAL(12,2),  
status ENUM('draft', 'active', 'paused', 'completed', 'cancelled') DEFAULT 'draft',  
assigned_commercial_id BIGINT,  
target_audience TEXT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_campaigns_status (status),  
INDEX idx_campaigns_type (type),  
INDEX idx_campaigns_commercial (assigned_commercial_id)  
);  
  
CREATE TABLE campaign_leads (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
campaign_id BIGINT NOT NULL,  
lead_id BIGINT NOT NULL,  
conversion_status ENUM('contacted', 'interested', 'qualified', 'converted', 'lost') DEFAULT 'contacted',  
date_added TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_campaign_leads_campaign (campaign_id),  
INDEX idx_campaign_leads_lead (lead_id),  
INDEX idx_campaign_leads_status (conversion_status)  
);  
  
CREATE TABLE email_templates (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
name VARCHAR(255) NOT NULL,  
subject VARCHAR(255) NOT NULL,  
content TEXT NOT NULL,  
template_type ENUM('marketing', 'transactional', 'notification') NOT NULL,  
is_active BOOLEAN DEFAULT TRUE,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_email_templates_type (template_type),  
INDEX idx_email_templates_active (is_active)  
);  
  
CREATE TABLE notifications (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
user_id BIGINT NOT NULL,  
title VARCHAR(255) NOT NULL,  
message TEXT NOT NULL,  
type ENUM('info', 'warning', 'error', 'success') DEFAULT 'info',  
is_read BOOLEAN DEFAULT FALSE,  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
reference_type VARCHAR(50),  
reference_id BIGINT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_notifications_user (user_id),  
INDEX idx_notifications_unread (is_read),  
INDEX idx_notifications_type (type),  
INDEX idx_notifications_reference (reference_type, reference_id)  
);  
  
CREATE TABLE activities (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
user_id BIGINT NOT NULL,  
activity_type ENUM('login', 'logout', 'create', 'update', 'delete', 'view', 'email', 'call') NOT NULL,  
reference_type VARCHAR(50),  
reference_id BIGINT,  
description TEXT NOT NULL,  
date_activity TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_activities_user (user_id),  
INDEX idx_activities_type (activity_type),  
INDEX idx_activities_date (date_activity),  
INDEX idx_activities_reference (reference_type, reference_id)  
);  
  
-- ============================================  
-- SECTION: GESTION ADMINISTRATIVE  
-- ============================================  
  
CREATE TABLE documents (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
reference_type VARCHAR(50) NOT NULL,  
reference_id BIGINT NOT NULL,  
document_name VARCHAR(255) NOT NULL,  
file_path VARCHAR(500) NOT NULL,  
document_type VARCHAR(100),  
uploaded_by_user_id BIGINT NOT NULL,  
upload_date TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
access_level ENUM('public', 'internal', 'restricted') DEFAULT 'internal',  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_documents_reference (reference_type, reference_id),  
INDEX idx_documents_type (document_type),  
INDEX idx_documents_uploader (uploaded_by_user_id),  
INDEX idx_documents_access (access_level)  
);  
  
CREATE TABLE approval_workflows (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
reference_type VARCHAR(50) NOT NULL,  
reference_id BIGINT NOT NULL,  
current_step TINYINT DEFAULT 1,  
status ENUM('pending', 'approved', 'rejected', 'cancelled') DEFAULT 'pending',  
created_by_user_id BIGINT NOT NULL,  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_approval_workflows_reference (reference_type, reference_id),  
INDEX idx_approval_workflows_status (status),  
INDEX idx_approval_workflows_creator (created_by_user_id)  
);  
  
CREATE TABLE workflow_steps (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
workflow_id BIGINT NOT NULL,  
step_order TINYINT NOT NULL,  
assigned_user_id BIGINT NOT NULL,  
action_required VARCHAR(255) NOT NULL,  
status ENUM('pending', 'completed', 'skipped') DEFAULT 'pending',  
completion_date TIMESTAMP NULL,  
comments TEXT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_workflow_steps_workflow (workflow_id),  
INDEX idx_workflow_steps_assigned (assigned_user_id),  
INDEX idx_workflow_steps_status (status)  
);  
  
CREATE TABLE settings (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
setting_key VARCHAR(100) UNIQUE NOT NULL,  
setting_value TEXT,  
description TEXT,  
is_system BOOLEAN DEFAULT FALSE,  
updated_by_user_id BIGINT,  
date_updated TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_settings_key (setting_key),  
INDEX idx_settings_system (is_system)  
);  
  
-- ============================================  
-- SECTION: GESTION QUALITÉ & SUPPORT  
-- ============================================  
  
CREATE TABLE knowledge_base (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
title VARCHAR(255) NOT NULL,  
content TEXT NOT NULL,  
category_id BIGINT,  
tags JSON,  
created_by_user_id BIGINT NOT NULL,  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
is_public BOOLEAN DEFAULT FALSE,  
view_count INT DEFAULT 0,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_knowledge_base_category (category_id),  
INDEX idx_knowledge_base_public (is_public),  
INDEX idx_knowledge_base_creator (created_by_user_id),  
FULLTEXT idx_knowledge_base_search (title, content)  
);  
  
CREATE TABLE faq (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
question TEXT NOT NULL,  
answer TEXT NOT NULL,  
category_id BIGINT,  
is_active BOOLEAN DEFAULT TRUE,  
created_by_user_id BIGINT NOT NULL,  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_faq_category (category_id),  
INDEX idx_faq_active (is_active),  
INDEX idx_faq_creator (created_by_user_id),  
FULLTEXT idx_faq_search (question, answer)  
);  
  
CREATE TABLE customer_feedback (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
client_id BIGINT NOT NULL,  
ticket_id BIGINT,  
rating TINYINT NOT NULL CHECK (rating >= 1 AND rating <= 5),  
feedback_text TEXT,  
feedback_type ENUM('service_quality', 'response_time', 'technical_expertise', 'overall') DEFAULT 'overall',  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
INDEX idx_customer_feedback_client (client_id),  
INDEX idx_customer_feedback_ticket (ticket_id),  
INDEX idx_customer_feedback_rating (rating),  
INDEX idx_customer_feedback_type (feedback_type)  
);  
  
CREATE TABLE quality_controls (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
intervention_id BIGINT NOT NULL,  
checklist_id BIGINT NOT NULL,  
performed_by_user_id BIGINT NOT NULL,  
status ENUM('pending', 'completed', 'failed') DEFAULT 'pending',  
completion_date TIMESTAMP NULL,  
notes TEXT,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
INDEX idx_quality_controls_intervention (intervention_id),  
INDEX idx_quality_controls_checklist (checklist_id),  
INDEX idx_quality_controls_performer (performed_by_user_id),  
INDEX idx_quality_controls_status (status)  
);  
  
CREATE TABLE checklists (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
name VARCHAR(255) NOT NULL,  
description TEXT,  
checklist_items JSON NOT NULL,  
applicable_to VARCHAR(100),  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_checklists_applicable (applicable_to)  
);  
  
-- ============================================  
-- SECTION: OTHER  
-- ============================================  
  
CREATE TABLE comments (  
id BIGINT PRIMARY KEY AUTO_INCREMENT,  
ticket_id BIGINT,  
user_id BIGINT NOT NULL,  
message TEXT NOT NULL,  
is_internal BOOLEAN DEFAULT TRUE,  
date_creation TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
attachments JSON,  
created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,  
updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,  
deleted_at TIMESTAMP NULL,  
INDEX idx_comments_ticket (ticket_id),  
INDEX idx_comments_user (user_id),  
INDEX idx_comments_internal (is_internal),  
INDEX idx_comments_date (date_creation)  
);