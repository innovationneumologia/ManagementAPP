👨‍⚕️ MEDICAL STAFF MANAGEMENT API
9. List All Medical Staff
javascript
GET /rest/v1/medical_staff?select=*,training_units(unit_name)&order=full_name.asc
10. Search Medical Staff
javascript
GET /rest/v1/medical_staff?or=(full_name.ilike.%{query}%,staff_id.ilike.%{query}%)&select=*
11. Add New Medical Staff
javascript
POST /rest/v1/medical_staff
Body: {
  staff_id: "RES-2024-001",
  full_name: "Dr. Anna Rodriguez",
  staff_type: "medical_resident",
  resident_category: "department_internal",
  training_year: 3,
  professional_email: "a.rodriguez@hospital.edu",
  work_phone: "+1234567890",
  primary_clinic: "ICU Pulmonary",
  can_supervise_residents: false,
  employment_status: "active"
}
12. Update Medical Staff Details
javascript
PATCH /rest/v1/medical_staff?id=eq.{staff_id}
Body: {
  training_year: 4,
  employment_status: "active",
  special_notes: "Promoted to chief resident"
}
13. Get Staff by Type
javascript
GET /rest/v1/medical_staff?staff_type=eq.attending_physician&select=*
14. Get Available Supervisors
javascript
GET /rest/v1/medical_staff?can_supervise_residents=eq.true&employment_status=eq.active&select=*
🏥 TRAINING UNITS API
15. List All Training Units
javascript
GET /rest/v1/training_units?select=*,medical_staff(full_name)&order=unit_name.asc
16. Create New Training Unit
javascript
POST /rest/v1/training_units
Body: {
  unit_code: "ICU-PULM-2",
  unit_name: "Pulmonary ICU - Tower B",
  department_name: "Pulmonology",
  maximum_residents: 6,
  default_supervisor_id: "{supervisor_uuid}",
  unit_description: "Second pulmonary ICU unit with 12 beds"
}
17. Update Unit Capacity/Status
javascript
PATCH /rest/v1/training_units?id=eq.{unit_id}
Body: {
  maximum_residents: 8,
  unit_status: "active"
}
18. Get Unit Current Occupancy
javascript
GET /rest/v1/resident_rotations?training_unit_id=eq.{unit_id}&rotation_status=eq.active&select=resident_id,medical_staff(full_name)
🔄 RESIDENT ROTATIONS API
19. List All Active Rotations
javascript
GET /rest/v1/resident_rotations?rotation_status=in.(active,scheduled)&select=*,medical_staff!resident_id(full_name),medical_staff!supervising_attending_id(full_name),training_units(unit_name)&order=rotation_start_date.desc
20. Create New Rotation Placement
javascript
POST /rest/v1/resident_rotations
Body: {
  rotation_id: "ROT-2024-001",
  resident_id: "{resident_uuid}",
  training_unit_id: "{unit_uuid}",
  supervising_attending_id: "{attending_uuid}",
  rotation_start_date: "2024-04-01",
  rotation_end_date: "2024-06-30",
  rotation_category: "clinical_rotation",
  rotation_status: "scheduled"
}
21. Update Rotation Dates/Status
javascript
PATCH /rest/v1/resident_rotations?id=eq.{rotation_id}
Body: {
  rotation_end_date: "2024-07-15", // Extend rotation
  rotation_status: "extended"
}
22. End Rotation Early
javascript
PATCH /rest/v1/resident_rotations?id=eq.{rotation_id}
Body: {
  rotation_end_date: "2024-05-15",
  rotation_status: "terminated_early",
  clinical_notes: "Rotation ended early due to..."
}
23. Get Rotations Ending Soon (Next 14 days)
javascript
GET /rest/v1/resident_rotations?rotation_end_date=gte.{today}&rotation_end_date=lte.{14_days_from_now}&rotation_status=eq.active&select=*
24. Get Resident's Rotation History
javascript
GET /rest/v1/resident_rotations?resident_id=eq.{resident_id}&select=*,training_units(unit_name),medical_staff!supervising_attending_id(full_name)&order=rotation_start_date.desc
📅 DAILY ASSIGNMENTS API
25. Get Assignments for Date Range
javascript
GET /rest/v1/daily_assignments?assignment_date=gte.{start_date}&assignment_date=lte.{end_date}&select=*,medical_staff!staff_member_id(full_name,staff_type),medical_staff!supervising_attending_id(full_name)&order=assignment_date.asc,start_time.asc
26. Create Daily Assignment
javascript
POST /rest/v1/daily_assignments
Body: {
  assignment_id: "ASSIGN-2024-001",
  staff_member_id: "{staff_uuid}",
  assignment_date: "2024-03-20",
  assignment_type: "outpatient_clinic",
  start_time: "08:00",
  end_time: "17:00",
  location_name: "Main Pulmonary Clinic",
  assignment_notes: "Regular clinic duty"
}
27. Update Assignment
javascript
PATCH /rest/v1/daily_assignments?id=eq.{assignment_id}
Body: {
  assignment_type: "icu_rounds",
  location_name: "ICU Pulmonary",
  start_time: "07:00",
  end_time: "19:00"
}
28. Delete Assignment
javascript
DELETE /rest/v1/daily_assignments?id=eq.{assignment_id}
29. Get Staff Member's Weekly Schedule
javascript
GET /rest/v1/daily_assignments?staff_member_id=eq.{staff_id}&assignment_date=gte.{monday}&assignment_date=lte.{sunday}&select=*
30. Check for Assignment Conflicts
javascript
GET /rest/v1/daily_assignments?staff_member_id=eq.{staff_id}&assignment_date=eq.{date}&or=(start_time.lte.{end_time}.and.end_time.gte.{start_time})&select=*
📞 ON-CALL SCHEDULE API
31. Get On-Call Schedule for Date Range
javascript
GET /rest/v1/oncall_schedule?duty_date=gte.{start_date}&duty_date=lte.{end_date}&select=*,medical_staff!primary_physician_id(full_name),medical_staff!backup_physician_id(full_name)&order=duty_date.asc
32. Assign On-Call Duty
javascript
POST /rest/v1/oncall_schedule
Body: {
  schedule_id: "ONCALL-2024-001",
  duty_date: "2024-03-20",
  shift_type: "primary_call",
  primary_physician_id: "{physician_uuid}",
  backup_physician_id: "{backup_uuid}",
  start_time: "17:00",
  end_time: "08:00",
  coverage_notes: "Covering pulmonary emergencies"
}
33. Update On-Call Assignment
javascript
PATCH /rest/v1/oncall_schedule?id=eq.{schedule_id}
Body: {
  primary_physician_id: "{new_physician_uuid}",
  backup_physician_id: "{new_backup_uuid}"
}
34. Get Who's On Call Today
javascript
GET /rest/v1/oncall_schedule?duty_date=eq.{today}&select=*,medical_staff!primary_physician_id(full_name,work_phone),medical_staff!backup_physician_id(full_name,work_phone)
🏖️ LEAVE MANAGEMENT API
35. Get All Leave Requests
javascript
GET /rest/v1/leave_requests?select=*,medical_staff(full_name,staff_type),app_users!reviewed_by(full_name)&order=leave_start_date.desc
36. Submit Leave Request
javascript
POST /rest/v1/leave_requests
Body: {
  request_id: "LEAVE-2024-001",
  staff_member_id: "{staff_uuid}",
  leave_category: "vacation_leave",
  leave_start_date: "2024-04-15",
  leave_end_date: "2024-04-22",
  total_days: 5,
  leave_reason: "Family vacation",
  coverage_required: true,
  approval_status: "pending_review"
}
37. Approve/Reject Leave Request
javascript
PATCH /rest/v1/leave_requests?id=eq.{request_id}
Body: {
  approval_status: "approved", // or 'rejected'
  reviewed_by: "{reviewer_uuid}",
  reviewed_at: "2024-03-15T10:00:00Z",
  review_notes: "Approved as per department policy"
}
38. Get Pending Leave Requests
javascript
GET /rest/v1/leave_requests?approval_status=eq.pending_review&select=*,medical_staff(full_name,primary_clinic)&order=leave_start_date.asc
39. Get Leave Calendar (Month View)
javascript
GET /rest/v1/leave_requests?leave_start_date=gte.{first_day_of_month}&leave_start_date=lte.{last_day_of_month}&approval_status=eq.approved&select=staff_member_id,leave_start_date,leave_end_date,medical_staff(full_name)
40. Assign Coverage for Leave
javascript
PATCH /rest/v1/leave_requests?id=eq.{request_id}
Body: {
  coverage_assigned: true,
  coverage_notes: "Covered by Dr. Smith from April 15-17, then by Dr. Johnson"
}
📢 ANNOUNCEMENTS API
41. Get Active Announcements
javascript
GET /rest/v1/department_announcements?publish_start_date=lte.{today}&and=(publish_end_date.is.null,or(publish_end_date.gte.{today}))&select=*&order=priority_level.desc,created_at.desc
42. Create New Announcement
javascript
POST /rest/v1/department_announcements
Body: {
  announcement_id: "ANN-2024-001",
  announcement_title: "New Bronchoscopy Equipment Training",
  announcement_content: "All staff are invited to training sessions...",
  announcement_type: "training_opportunity",
  priority_level: "high",
  visible_to_roles: ["viewing_doctor", "resident_manager"],
  publish_start_date: "2024-03-20",
  publish_end_date: "2024-03-31",
  created_by: "{user_uuid}"
}
43. Update Announcement
javascript
PATCH /rest/v1/department_announcements?id=eq.{announcement_id}
Body: {
  publish_end_date: "2024-04-15",
  priority_level: "normal"
}
44. Archive Announcement
javascript
PATCH /rest/v1/department_announcements?id=eq.{announcement_id}
Body: {
  publish_end_date: "{today}"
}
📊 REPORTING & ANALYTICS API
45. Department Dashboard Stats
javascript
-- This would be a database function, but here's the concept:
GET /rest/v1/rpc/get_dashboard_stats?user_id=eq.{user_id}

-- Returns: {
--   total_staff: 42,
--   active_residents: 12,
--   unit_occupancy_rate: 85,
--   pending_leave_requests: 3,
--   upcoming_rotation_ends: 2,
--   on_call_today: "Dr. Johnson"
-- }
46. Unit Capacity Report
javascript
GET /rest/v1/training_units?select=unit_name,maximum_residents,(select:resident_rotations(count))&unit_status=eq.active
47. Resident Placement Report
javascript
GET /rest/v1/resident_rotations?rotation_status=eq.active&select=medical_staff!resident_id(full_name,resident_category),training_units(unit_name),medical_staff!supervising_attending_id(full_name),rotation_end_date&order=training_units(unit_name).asc
48. Leave Coverage Report
javascript
GET /rest/v1/leave_requests?approval_status=eq.approved&leave_end_date=gte.{today}&select=staff_member_id,leave_start_date,leave_end_date,coverage_assigned,medical_staff(full_name,primary_clinic)&order=leave_start_date.asc
49. Duty Hour Compliance Report
javascript
-- This requires a database function to calculate hours
GET /rest/v1/rpc/get_duty_hours_report?start_date=2024-03-01&end_date=2024-03-31

-- Returns per resident:
-- {
--   resident_name: "Dr. Anna Rodriguez",
--   total_hours: 78,
--   max_weekly_hours: 80,
--   compliance_status: "compliant",
--   alerts: []
-- }
50. Export Data to CSV/Excel
javascript
-- Using Supabase's CSV export feature
GET /rest/v1/{table_name}?select=*&headers=prefer
Accept: text/csv
🔧 SYSTEM MANAGEMENT API
51. Get Audit Logs (Admin Only)
javascript
GET /rest/v1/system_audit_log?select=*&order=log_timestamp.desc&limit=100
52. System Health Check
javascript
GET /rest/v1/rpc/health_check

-- Returns: {
--   database_status: "healthy",
--   active_connections: 5,
--   last_backup: "2024-03-15T02:00:00Z",
--   uptime_days: 45
-- }
53. Backup Database (Admin Only)
javascript
POST /rest/v1/rpc/create_backup
Body: {
  backup_name: "daily_backup_2024_03_15"
}
