
---
# Users

- [] admin - First name, Name, Email, status, password

external_technician  - First name, Name, Email, disponibilities, location, status,  password, Partenaire_id

internal technician - First name, Name, Email, disponibilities, location, status,  password

chef projet - First name, Name, Email, disponibilities, location, status, password

support -  First name, Name, Email, status,  password

adv - FIrst name, Name, Email, status, password

commercial - First name, Name, Email, status, password

compta - First name, Name, Email, status, password

clients -  company_name, contact_firstname, contact_lastname, email, phone, adresse, city, postal_code, country, status

partner - patner_name, contact_firstname, contact_lastname, email, phone, adresse, city, postal_code, country, status

user_roles - id, role_name, permissions

---
# Ticket / SAV

ticket - Title, description, domain, priority, emergency_level, deadline (nullable), status, partenaire_id, client_id, assigned_technicien_id, created_by_user_id, date_creation, estimated_hours, actual_hours, date_last_update, date_end

ticket_history - ticket_id, user_id, action, old_values, new values, comment, date_action

---
# Planning & intervention

intervention - ticket_id, intervention_date, start_datetime, end_datetime, external_technician_id, chefprojet_id, status, type_intervention, location, notes


documentation - title, description, photo, contrainte, technician_note, chef_projet_note, external_technician_id, chef_projet_id, intervention_date, intervention_starting_date, intervention_ending_date, 

---
# Gestion adv & Commandes

leads - company_name, contact_person, email, phone, source, status, assigned_comercial_id, qualification_score, date_creation, date_last_contact

opportunities - lead_id, client_id, title, description, estimated_values, probability, stage, 
expected_close_date, assigned_commercial_id, date_creation

quotes - opportunity_id, client_id, quote_number, title, description, total_amount, tax_amount, 
status, validity_date, created_by_user_id, date_creation 

quote_items - quote_id, 

products_services - name, description, category_id, unit_price, cost_price, is_active, sku 

product_categories - name, description, parent_category_id

---
# Gestion ADV & Commandes

orders - client_id, quote_id, order_number, status, order_date, delivery_date, shipping_address, billing_address, total_amount, assigned_adv_id, notes

order_items - order_id, product_service_id, description, quantity, unit_price, total_price

deliveries - order_id, delivery_date, delivery_address, carrier, tracking_number, status, delivered_by_user_id

invoices - order_id, client_id, invoice_number, invoice_date, due_date, total_amount, tax_amount, status, payment_terms

invoice_items - invoice_id, description, quantity, unit_price, tax_rate, total_price

payments - invoice_id, payment_date, amount, payment_method, reference, status, processed_by_user_id

---
# Gestion projet & planning

projects - client_id, name, description, start_date, end_date, status, budget, assigned_chef_projet_id, completion_percentage

project_tasks - project_id, title, description, assigned_user_id, start_date, due_date, status, priority, estimated_hours, actual_hours

project_milestones - project_id, title, description, due_date, status, completion_date

resource_allocation - project_id, user_id, allocated_hours, start_date, end_date, rate_per_hour

timesheets - user_id, project_id, task_id, date, hours_worked, description, is_billable, hourly_rate

---
# Gestion matériel & stock

equipment - name, serial_number, model, brand, category_id, status, location, assigned_client_id, purchase_date, warranty_end_date

equipment_categories - name, description

stock_items - name, description, sku, category_id, current_stock, min_stock, max_stock, unit_cost, supplier_id

stock_movements - stock_item_id, movement_type, quantity, reference_type, reference_id, user_id, date_movement, notes

suppliers - company_name, contact_person, email, phone, address, payment_terms, status

---
# Gestion contractuelle

contracts - client_id, contract_number, title, start_date, end_date, value, status, contract_type, renewal_terms, assigned_user_id

contract_services - contract_id, service_description, sla_terms, billing_frequency, unit_price

sla_metrics - contract_id, metric_name, target_value, measurement_unit, penalty_clause

maintenance_schedules - contract_id, equipment_id, frequency, next_maintenance_date, assigned_technician_id

---
# Gestion communications & marketing

campaigns - name, type, start_date, end_date, budget, status, assigned_commercial_id, target_audience

campaign_leads - campaign_id, lead_id, conversion_status, date_added

email_templates - name, subject, content, template_type, is_active

notifications - user_id, title, message, type, is_read, date_creation, reference_type, reference_id

activities - user_id, activity_type, reference_type, reference_id, description, date_activity

---
# Gestion administrative

documents - reference_type, reference_id, document_name, file_path, document_type, uploaded_by_user_id, upload_date, access_level

approval_workflows - reference_type, reference_id, current_step, status, created_by_user_id, date_creation

workflow_steps - workflow_id, step_order, assigned_user_id, action_required, status, completion_date, comments

settings - setting_key, setting_value, description, is_system, updated_by_user_id, date_updated

---
# Gestion qualité & support

knowledge_base - title, content, category_id, tags, created_by_user_id, date_creation, is_public, view_count

faq - question, answer, category_id, is_active, created_by_user_id, date_creation

customer_feedback - client_id, ticket_id, rating, feedback_text, feedback_type, date_creation

quality_controls - intervention_id, checklist_id, performed_by_user_id, status, completion_date, notes

checklists - name, description, checklist_items, applicable_to

---
# OTHER

documentation - intervention_id, title, description, photos, constraints, technician_notes, document_type, file_path, date_creation

comments - ticket_id, user_id, message, is_internal, date_creation, attachments

disponibilites - user_id, day_of_week, start_time, end_time, is_available