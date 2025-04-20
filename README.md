# Online-resume-builder-
This is the code for online resume builder so we can easily build our resume 

<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>Resume Builder</title>
    <!-- Include jsPDF library -->
    <script src="https://cdnjs.cloudflare.com/ajax/libs/jspdf/2.5.1/jspdf.umd.min.js"></script>
    <style>
        body {
            font-family: Arial, sans-serif;
            max-width: 800px;
            margin: 0 auto;
            padding: 20px;
            background-color: #f4f4f4;
        }
        .resume-builder {
            background-color: white;
            padding: 30px;
            border-radius: 8px;
            box-shadow: 0 4px 6px rgba(0, 0, 0, 0.1);
        }
        .section {
            margin-bottom: 20px;
        }
        .section h2 {
            border-bottom: 2px solid #333;
            padding-bottom: 10px;
            margin-bottom: 15px;
        }
        input, textarea {
            width: 100%;
            padding: 10px;
            margin-bottom: 10px;
            border: 1px solid #ddd;
            border-radius: 4px;
        }
        button {
            background-color: #4CAF50;
            color: white;
            border: none;
            padding: 10px 20px;
            border-radius: 4px;
            cursor: pointer;
            margin-right: 10px;
            transition: background-color 0.3s;
        }
        button:hover {
            background-color: #45a049;
        }
        #preview {
            margin-top: 20px;
            background-color: white;
            padding: 20px;
            border-radius: 8px;
        }
        .preview-section {
            margin-bottom: 15px;
        }
        .button-group {
            display: flex;
            gap: 10px;
            justify-content: flex-end;
            margin-top: 20px;
        }
    </style>
</head>
<body>
    <div class="resume-builder">
        <div id="personalInfo" class="section">
            <h2>Personal Information</h2>
            <input type="text" id="name" placeholder="Full Name">
            <input type="email" id="email" placeholder="Email">
            <input type="tel" id="phone" placeholder="Phone">
            <textarea id="summary" placeholder="Professional Summary"></textarea>
        </div>

        <div id="experience" class="section">
            <h2>Experience</h2>
            <div id="experienceList">
                <input type="text" class="company" placeholder="Company">
                <input type="text" class="position" placeholder="Position">
                <input type="text" class="duration" placeholder="Duration">
                <textarea class="description" placeholder="Job Description"></textarea>
            </div>
            <button onclick="addExperience()">Add Another Experience</button>
        </div>

        <div id="education" class="section">
            <h2>Education</h2>
            <div id="educationList">
                <input type="text" class="school" placeholder="School Name">
                <input type="text" class="degree" placeholder="Degree">
                <input type="text" class="year" placeholder="Year">
            </div>
            <button onclick="addEducation()">Add Another Education</button>
        </div>

        <div id="skills" class="section">
            <h2>Skills</h2>
            <div id="skillsList">
                <input type="text" class="skill" placeholder="Enter a skill">
            </div>
            <button onclick="addSkill()">Add Another Skill</button>
        </div>

        <div class="button-group">
            <button onclick="generateResume()">Preview Resume</button>
            <button onclick="downloadJSON()">Download JSON</button>
            <button onclick="downloadPDF()">Download PDF</button>
        </div>

        <div id="preview"></div>
    </div>

    <script>
        function addExperience() {
            const list = document.getElementById('experienceList');
            const newExperience = document.createElement('div');
            newExperience.innerHTML = `
                <input type="text" class="company" placeholder="Company">
                <input type="text" class="position" placeholder="Position">
                <input type="text" class="duration" placeholder="Duration">
                <textarea class="description" placeholder="Job Description"></textarea>
            `;
            list.appendChild(newExperience);
        }

        function addEducation() {
            const list = document.getElementById('educationList');
            const newEducation = document.createElement('div');
            newEducation.innerHTML = `
                <input type="text" class="school" placeholder="School Name">
                <input type="text" class="degree" placeholder="Degree">
                <input type="text" class="year" placeholder="Year">
            `;
            list.appendChild(newEducation);
        }

        function addSkill() {
            const list = document.getElementById('skillsList');
            const newSkill = document.createElement('input');
            newSkill.type = 'text';
            newSkill.classList.add('skill');
            newSkill.placeholder = 'Enter a skill';
            list.appendChild(newSkill);
        }

        function getResumeData() {
            return {
                personalInfo: {
                    name: document.getElementById('name').value,
                    email: document.getElementById('email').value,
                    phone: document.getElementById('phone').value,
                    summary: document.getElementById('summary').value
                },
                experience: Array.from(document.querySelectorAll('#experienceList > div')).map(exp => ({
                    company: exp.querySelector('.company').value,
                    position: exp.querySelector('.position').value,
                    duration: exp.querySelector('.duration').value,
                    description: exp.querySelector('.description').value
                })),
                education: Array.from(document.querySelectorAll('#educationList > div')).map(edu => ({
                    school: edu.querySelector('.school').value,
                    degree: edu.querySelector('.degree').value,
                    year: edu.querySelector('.year').value
                })),
                skills: Array.from(document.querySelectorAll('.skill')).map(skill => skill.value)
            };
        }

        function generateResume() {
            const resumeData = getResumeData();
            const previewDiv = document.getElementById('preview');
            previewDiv.innerHTML = `
                <h1>${resumeData.personalInfo.name}</h1>
                <p>${resumeData.personalInfo.email} | ${resumeData.personalInfo.phone}</p>
                <p>${resumeData.personalInfo.summary}</p>

                <h2>Experience</h2>
                ${resumeData.experience.map(exp => `
                    <div class="preview-section">
                        <h3>${exp.position}</h3>
                        <p>${exp.company} | ${exp.duration}</p>
                        <p>${exp.description}</p>
                    </div>
                `).join('')}

                <h2>Education</h2>
                ${resumeData.education.map(edu => `
                    <div class="preview-section">
                        <h3>${edu.degree}</h3>
                        <p>${edu.school} | ${edu.year}</p>
                    </div>
                `).join('')}

                <h2>Skills</h2>
                <p>${resumeData.skills.join(', ')}</p>
            `;
        }

        function downloadJSON() {
            const resumeData = getResumeData();
            const blob = new Blob([JSON.stringify(resumeData, null, 2)], {type: 'application/json'});
            const link = document.createElement('a');
            link.href = URL.createObjectURL(blob);
            link.download = `${resumeData.personalInfo.name.replace(/\s+/g, '_')}_resume.json`;
            link.click();
        }

        function downloadPDF() {
            const { jsPDF } = window.jspdf;
            const doc = new jsPDF();
            const resumeData = getResumeData();
            let yPosition = 20;
            const margin = 20;

            // Add personal information
            doc.setFontSize(24);
            doc.text(resumeData.personalInfo.name, margin, yPosition);
            yPosition += 10;

            doc.setFontSize(12);
            doc.text(`${resumeData.personalInfo.email} | ${resumeData.personalInfo.phone}`, margin, yPosition);
            yPosition += 10;

            // Add summary with word wrap
            const summaryLines = doc.splitTextToSize(resumeData.personalInfo.summary, doc.internal.pageSize.width - 40);
            doc.text(summaryLines, margin, yPosition);
            yPosition += summaryLines.length * 7 + 10;

            // Add experience section
            doc.setFontSize(16);
            doc.text("Experience", margin, yPosition);
            yPosition += 10;

            resumeData.experience.forEach((exp) => {
                doc.setFontSize(14);
                doc.text(exp.position, margin, yPosition);
                yPosition += 7;
                doc.setFontSize(12);
                doc.text(`${exp.company} | ${exp.duration}`, margin, yPosition);
                yPosition += 7;
                const descLines = doc.splitTextToSize(exp.description, doc.internal.pageSize.width - 40);
                doc.text(descLines, margin, yPosition);
                yPosition += descLines.length * 7 + 10;
            });

            // Add education section
            doc.setFontSize(16);
            doc.text("Education", margin, yPosition);
            yPosition += 10;

            resumeData.education.forEach((edu) => {
                doc.setFontSize(14);
                doc.text(edu.degree, margin, yPosition);
                yPosition += 7;
                doc.setFontSize(12);
                doc.text(`${edu.school} | ${edu.year}`, margin, yPosition);
                yPosition += 10;
            });

            // Add skills section
            doc.setFontSize(16);
            doc.text("Skills", margin, yPosition);
            yPosition += 10;

            doc.setFontSize(12);
            const skillsText = resumeData.skills.filter(skill => skill.trim()).join(", ");
            const skillsLines = doc.splitTextToSize(skillsText, doc.internal.pageSize.width - 40);
            doc.text(skillsLines, margin, yPosition);

            // Save the PDF
            doc.save(`${resumeData.personalInfo.name.replace(/\s+/g, '_')}_resume.pdf`);
        }
    </script>
</body>
</html>