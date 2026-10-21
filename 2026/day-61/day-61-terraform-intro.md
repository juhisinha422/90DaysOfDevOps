%-------------------------
% ATS-friendly resume: single column, no tables, no icons, no images
% Compile with pdfLaTeX (Overleaf default)
%-------------------------
\documentclass[10.5pt,letterpaper]{article}

\usepackage[T1]{fontenc}
\usepackage[utf8]{inputenc}
\usepackage{lmodern}
\usepackage[margin=0.7in]{geometry}
\usepackage{enumitem}
\usepackage[hidelinks]{hyperref}
\usepackage{titlesec}
\usepackage{xcolor}

% Make text extractable / ATS-parsable
\input{glyphtounicode}
\pdfgentounicode=1

\pagestyle{empty}
\setlength{\parindent}{0pt}
\setlength{\parskip}{0pt}
\raggedbottom

% Section style: plain text heading with a rule
\titleformat{\section}{\large\bfseries\scshape}{}{0em}{}[\titlerule]
\titlespacing*{\section}{0pt}{9pt}{5pt}

% Bullet style
\setlist[itemize]{leftmargin=0.2in, itemsep=1.5pt, topsep=2pt, parsep=0pt}

% Helper: role line  (title | dates) then (company | location)
\newcommand{\roleheader}[4]{%
  \textbf{#1} \hfill #2\\
  \textit{#3} \hfill \textit{#4}\vspace{2pt}
}

\begin{document}

%---------- HEADER ----------
\begin{center}
  {\LARGE \textbf{Juhi Sinha}}\\[3pt]
  DevOps Engineer | AWS Certified DevOps Engineer -- Professional\\[2pt]
  Bengaluru, Karnataka, India | +91 8120377675 | juhisinha099@gmail.com\\[2pt]
  \href{https://linkedin.com/in/juhi-sinha-2aa280176}{linkedin.com/in/juhi-sinha-2aa280176} | \href{https://github.com/juhisinha422}{github.com/juhisinha422}
\end{center}

%---------- SUMMARY ----------
\section{Professional Summary}
AWS Certified DevOps Engineer -- Professional with 4+ years of experience in Environment Management and DevOps, building and operating secure, scalable AWS infrastructure across Dev, UAT, Pre-Prod, and Production. Hands-on with Terraform and CloudFormation (Infrastructure as Code), Docker, Kubernetes (Amazon EKS), CI/CD pipelines (Jenkins, YAML pipelines), and Linux administration. Strong in networking and cloud security fundamentals, incident management, root cause analysis, automation (Bash, Python), and observability (Prometheus, Grafana, ELK), sustaining 99.9\% platform availability in enterprise-scale environments.

%---------- SKILLS ----------
\section{Technical Skills}
\textbf{Cloud (AWS):} EC2, S3, RDS, IAM, VPC, EKS, ECS, Lambda, Load Balancers\\
\textbf{Infrastructure as Code:} Terraform, AWS CloudFormation\\
\textbf{Containers \& Orchestration:} Docker, Kubernetes, Amazon EKS, ECS, Helm\\
\textbf{CI/CD:} Jenkins, AWS DevOps, YAML Pipelines, Trunk-Based Deployment, Nexus\\
\textbf{Linux \& Networking:} Linux Administration and Troubleshooting, VPC Peering, Bastion Hosts, NAT Gateway, Load Balancers, NSGs, Firewalls\\
\textbf{Security:} IAM, Secure Configuration, Network Security, SonarQube\\
\textbf{Monitoring \& Observability:} Prometheus, Grafana, ELK Stack, Alerting\\
\textbf{Automation \& Scripting:} Bash/Shell Scripting, Python\\
\textbf{Version Control:} Git, GitHub\\
\textbf{Practices:} Environment Management, Incident Management, Root Cause Analysis, Agile

%---------- EXPERIENCE ----------
\section{Work Experience}

\roleheader{DevOps Engineer}{Feb 2022 -- Present}{Tata Consultancy Services (TCS)}{India}
\begin{itemize}
  \item Managed Dev, UAT, Pre-Prod, and Production environments on AWS, ensuring consistent, repeatable, and secure environment delivery.
  \item Automated AWS infrastructure provisioning using Terraform modules integrated into CI/CD workflows, reducing environment setup time by 60\%.
  \item Designed trunk-based CI/CD pipelines in YAML across all environments, increasing deployment frequency by 40\%.
  \item Orchestrated 10+ containerized workloads on Amazon EKS using Kubernetes manifests with auto-scaling, rolling updates, and health checks, improving resource utilization by 45\%.
  \item Containerized applications using Docker multi-stage builds, reducing image size by 40\% and speeding up container startup and rollouts.
  \item Automated 15+ operational workflows using Bash/Shell scripting and Terraform, cutting manual effort by 50\% and improving operational reliability.
  \item Built 20+ Grafana dashboards and Prometheus alerting rules for proactive issue detection, supporting 99.9\% platform availability.
  \item Led incident management, recovery, and root cause analysis, implementing preventive fixes to improve platform reliability and reduce repeat incidents.
  \item Administered and troubleshot Linux-based systems and core AWS services (EC2, S3, RDS, VPC, IAM, Lambda).
  \item Designed secure cloud networking using VPC Peering, Bastion Hosts, NAT Gateways, and Load Balancers, reducing security risk by 70\%.
  \item Administered 10+ microservice repositories on Git/GitHub with branch policies and code review practices, reducing merge conflicts by 30\%.
  \item Collaborated with Software Engineering, Cloud, and IT Operations teams in an agile setup to improve operational readiness, observability, and security across enterprise-scale cloud environments.
\end{itemize}

\vspace{4pt}
\roleheader{Cloud Support Engineer}{Aug 2021 -- Jan 2022}{Tata Consultancy Services (TCS)}{India}
\begin{itemize}
  \item Provided Level 1 and Level 2 support for AWS-based cloud infrastructure.
  \item Managed and monitored virtual machines, storage, and network security groups (NSGs).
  \item Performed root cause analysis for incidents and implemented preventive solutions.
\end{itemize}

%---------- CERTIFICATIONS ----------
\section{Certifications}
\begin{itemize}
  \item AWS Certified DevOps Engineer -- Professional, Amazon Web Services
\end{itemize}

%---------- ACHIEVEMENTS ----------
\section{Achievements}
\begin{itemize}
  \item Best Performer Award, InvestIndia-NSWS (National Single Window System) Project
  \item On-The-Spot Award for swift action and effective solutions, InvestIndia-NSWS Project
\end{itemize}

%---------- EDUCATION ----------
\section{Education}
\textbf{B.Tech, Computer Science} \hfill 2017 -- 2021\\
Bansal Institute of Science \& Technology, Bhopal, Madhya Pradesh

\end{document}
