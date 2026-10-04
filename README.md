import 'package:flutter/material.dart';

void main() {
  runApp(
    const MaterialApp(
      debugShowCheckedModeBanner: false,
      home: RiskInspectionReportMaker(),
    ),
  );
}

class RiskInspectionReportMaker extends StatefulWidget {
  const RiskInspectionReportMaker({super.key});

  @override
  State<RiskInspectionReportMaker> createState() =>
      _RiskInspectionReportMakerState();
}

class _RiskInspectionReportMakerState extends State<RiskInspectionReportMaker> {
  final _formKey = GlobalKey<FormState>();
  final _inspectorController = TextEditingController();
  final _locationController = TextEditingController();
  final _hazardController = TextEditingController();
  final _actionController = TextEditingController();

  String _riskLevel = 'Medium';
  String? _report;

  @override
  void dispose() {
    _inspectorController.dispose();
    _locationController.dispose();
    _hazardController.dispose();
    _actionController.dispose();
    super.dispose();
  }

  void _generateReport() {
    if (!_formKey.currentState!.validate()) return;

    final date = DateTime.now();
    final formattedDate =
        '${date.year}-${date.month.toString().padLeft(2, '0')}-${date.day.toString().padLeft(2, '0')}';

    setState(() {
      _report = '''
Risk Inspection Report
Date: $formattedDate
Inspector: ${_inspectorController.text.trim()}
Location: ${_locationController.text.trim()}
Risk Level: $_riskLevel
Hazard Details: ${_hazardController.text.trim()}
Recommended Actions: ${_actionController.text.trim()}
''';
    });
  }

  @override
  Widget build(BuildContext context) {
    return Scaffold(
      appBar: AppBar(title: const Text('Risk Inspection Report Maker')),
      body: SingleChildScrollView(
        padding: const EdgeInsets.all(16),
        child: Form(
          key: _formKey,
          child: Column(
            crossAxisAlignment: CrossAxisAlignment.start,
            children: [
              TextFormField(
                controller: _inspectorController,
                decoration: const InputDecoration(labelText: 'Inspector Name'),
                validator: (value) => value == null || value.trim().isEmpty
                    ? 'Please enter inspector name'
                    : null,
              ),
              TextFormField(
                controller: _locationController,
                decoration: const InputDecoration(labelText: 'Inspection Location'),
                validator: (value) => value == null || value.trim().isEmpty
                    ? 'Please enter inspection location'
                    : null,
              ),
              DropdownButtonFormField<String>(
                value: _riskLevel,
                decoration: const InputDecoration(labelText: 'Risk Level'),
                items: const ['Low', 'Medium', 'High', 'Critical']
                    .map((level) => DropdownMenuItem(value: level, child: Text(level)))
                    .toList(),
                onChanged: (value) {
                  if (value != null) {
                    setState(() => _riskLevel = value);
                  }
                },
              ),
              TextFormField(
                controller: _hazardController,
                maxLines: 3,
                decoration: const InputDecoration(labelText: 'Hazard Details'),
                validator: (value) => value == null || value.trim().isEmpty
                    ? 'Please describe the hazard'
                    : null,
              ),
              TextFormField(
                controller: _actionController,
                maxLines: 3,
                decoration:
                    const InputDecoration(labelText: 'Recommended Actions'),
                validator: (value) => value == null || value.trim().isEmpty
                    ? 'Please provide recommended actions'
                    : null,
              ),
              const SizedBox(height: 16),
              ElevatedButton(
                onPressed: _generateReport,
                child: const Text('Generate Report'),
              ),
              if (_report != null) ...[
                const SizedBox(height: 20),
                const Text(
                  'Generated Report',
                  style: TextStyle(fontSize: 16, fontWeight: FontWeight.bold),
                ),
                const SizedBox(height: 8),
                Container(
                  width: double.infinity,
                  padding: const EdgeInsets.all(12),
                  decoration: BoxDecoration(
                    border: Border.all(color: Colors.grey.shade400),
                    borderRadius: BorderRadius.circular(8),
                  ),
                  child: Text(_report!),
                ),
              ],
            ],
          ),
        ),
      ),
    );
  }
}
