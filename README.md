from reportlab.lib.pagesizes import A4
from reportlab.platypus import (BaseDocTemplate, PageTemplate, Frame, Paragraph, Spacer, PageBreak,
    Preformatted, Table, TableStyle, KeepTogether, ListFlowable, ListItem)
from reportlab.lib.styles import ParagraphStyle
from reportlab.lib import colors
from reportlab.lib.units import mm
from xml.sax.saxutils import escape

BLUE=colors.HexColor("#1F4E8C"); LBLUE=colors.HexColor("#E8F0FA"); GREY=colors.HexColor("#5F6B7A")
LGREY=colors.HexColor("#F2F4F7"); MGREY=colors.HexColor("#CBD2DA"); WHITE=colors.white
W,H=A4

body=ParagraphStyle("b",fontName="Helvetica",fontSize=10.5,leading=16,textColor=colors.HexColor("#333B45"),spaceAfter=6)
h2=ParagraphStyle("h2",fontName="Helvetica-Bold",fontSize=15,leading=19,textColor=BLUE,spaceBefore=14,spaceAfter=6)
h3=ParagraphStyle("h3",fontName="Helvetica-Bold",fontSize=11.5,leading=15,textColor=GREY,spaceBefore=6,spaceAfter=4)
code=ParagraphStyle("c",fontName="Courier",fontSize=8.6,leading=11.5,textColor=colors.HexColor("#1F2A37"))
li=ParagraphStyle("li",parent=body,spaceAfter=2)
link=lambda u,t: f'<a href="{u}" color="#1F4E8C"><u>{t}</u></a>'

def H2(t):
    return [Paragraph(escape(t),h2),
            Table([[""]],colWidths=[W-40*mm],rowHeights=[1.2],style=[("BACKGROUND",(0,0),(-1,-1),MGREY)]),Spacer(1,6)]
def P(t): return Paragraph(t,body)
def CODE(t):
    t=t.strip("\n")
    tb=Table([[Preformatted(t,code)]],colWidths=[W-40*mm])
    tb.setStyle(TableStyle([("BACKGROUND",(0,0),(-1,-1),LGREY),("LINEBEFORE",(0,0),(0,-1),3,BLUE),
        ("BOX",(0,0),(-1,-1),0.5,MGREY),("LEFTPADDING",(0,0),(-1,-1),10),("TOPPADDING",(0,0),(-1,-1),7),("BOTTOMPADDING",(0,0),(-1,-1),7)]))
    return [tb,Spacer(1,8)]
def LIST(items,bullet=None):
    return ListFlowable([ListItem(Paragraph(i,li),leftIndent=14) for i in items],
        bulletType="1" if bullet=="n" else "bullet",start=1 if bullet=="n" else "•",
        bulletColor=BLUE,bulletFontName="Helvetica-Bold",leftIndent=16,bulletFontSize=9.5)
def IMG(title,url):
    tb=Table([[Paragraph('<font size=8.5 color="#5F6B7A">Diagram image: </font>'+link(url,"view online"),
        ParagraphStyle("i",parent=body,alignment=1,spaceAfter=0))]],colWidths=[W-40*mm],rowHeights=[28*mm])
    tb.setStyle(TableStyle([("BACKGROUND",(0,0),(-1,-1),LBLUE),("BOX",(0,0),(-1,-1),0.8,BLUE),("VALIGN",(0,0),(-1,-1),"MIDDLE")]))
    return [Paragraph(escape(title),h3),tb,Spacer(1,8)]

def page(c,d):
    c.saveState()
    c.setFillColor(BLUE); c.rect(0,H-8*mm,W,8*mm,stroke=0,fill=1)
    c.setFillColor(MGREY); c.rect(0,0,W,10*mm,stroke=0,fill=1)
    c.setFillColor(GREY); c.setFont("Helvetica",8)
    c.drawString(20*mm,4*mm,"Customer Personality Segmentation"); c.drawRightString(W-20*mm,4*mm,f"Page {d.page}")
    c.restoreState()

doc=BaseDocTemplate("Customer_Personality_Segmentation_README.pdf",pagesize=A4,
    leftMargin=20*mm,rightMargin=20*mm,topMargin=22*mm,bottomMargin=20*mm,title="Customer Personality Segmentation")
doc.addPageTemplates([PageTemplate(frames=[Frame(20*mm,16*mm,W-40*mm,H-38*mm,id="f",leftPadding=0,rightPadding=0,topPadding=0,bottomPadding=0)],onPage=page)])

S=[]
# Title banner
t=Table([[Paragraph('<font color="white" size=24><b>Customer Personality Segmentation</b></font>',ParagraphStyle("t",leading=30))],
         [Paragraph('<font color="#E8F0FA" size=10.5>Machine Learning project: cluster customers and predict their segment</font>',ParagraphStyle("s",leading=14))]],
        colWidths=[W-40*mm])
t.setStyle(TableStyle([("BACKGROUND",(0,0),(-1,-1),BLUE),("LEFTPADDING",(0,0),(-1,-1),14),("TOPPADDING",(0,0),(0,0),16),("BOTTOMPADDING",(0,1),(0,1),16)]))
S+=[t,Spacer(1,10)]

S+=H2("Problem Statement")
S.append(P("In this data science project, you will build a machine learning system which will be able to predict the personality of the customer using machine learning algorithms. This project will be very useful for malls, various stores and product based companies. Based on customer's personal details and purchase details, we can cluster them and we can predict the customer's cluster number using classification techniques."))

S+=H2("Solution Proposed")
S.append(P("Now the question is how to dynamically predict the cluster of the customer? One of the approaches we can use is the machine learning approach, where we cluster the customers based on the details we have and predict the cluster type based on domain knowledge, leveraging previous customer data to predict the cluster."))
S.append(P("<b>Dataset used:</b> "+link("https://github.com/entbappy/Branching-tutorial/blob/master/marketing_campaign.zip","Dataset Link")))

S+=H2("Tech Stack Used")
S.append(LIST(["Python","FastAPI","Machine learning algorithms","Docker","MongoDB"],"n"))
S+=H2("Infrastructure Required")
S.append(LIST(["AWS S3","Azure","Github Actions"],"n"))

S+=H2("How to Run")
S.append(P("Before you run this project make sure you have a MongoDB Atlas account and you have the shipping dataset in it."))
S.append(Paragraph("Step 1. Cloning the repository",h3)); S+=CODE("git clone https://github.com/Machine-Learning-01/Customer_segmentation.git")
S.append(Paragraph("Step 2. Create a conda environment",h3)); S+=CODE("conda create --prefix venv python=3.7 -y\n\nconda activate venv/")
S.append(Paragraph("Step 3. Install the requirements",h3)); S+=CODE("pip install -r requirements.txt")
S.append(Paragraph("Step 4. Export the environment variables",h3))
S+=CODE("export AWS_ACCESS_KEY_ID=<AWS_ACCESS_KEY_ID>\n\nexport AWS_SECRET_ACCESS_KEY=<AWS_SECRET_ACCESS_KEY>\n\nexport AWS_DEFAULT_REGION=<AWS_DEFAULT_REGION>\n\nexport MONGODB_URL=<MONGODB_URL>")
S.append(Paragraph("Step 5. Run the application server",h3)); S+=CODE("python app.py")
S.append(Paragraph("Step 6. Train application",h3)); S+=CODE("http://localhost:5000/train")
S.append(Paragraph("Step 7. Prediction application",h3)); S+=CODE("http://localhost:5000/predict")

S+=H2("Run Locally")
S.append(LIST(["Check if the Dockerfile is available in the project directory","Build the Docker image","Run the Docker image"],"n"))
S.append(Spacer(1,6))
S+=CODE("# Build\ndocker build --build-arg AWS_ACCESS_KEY_ID=<AWS_ACCESS_KEY_ID> \\\n  --build-arg AWS_SECRET_ACCESS_KEY=<AWS_SECRET_ACCESS_KEY> \\\n  --build-arg AWS_DEFAULT_REGION=<AWS_DEFAULT_REGION> \\\n  --build-arg MONGODB_URL=<MONGODB_URL> .\n\n# Run\ndocker run -d -p 5000:5000 <IMAGE_NAME>")

S.append(PageBreak())
S+=H2("Architecture")
S+=IMG("Project Architecture","https://user-images.githubusercontent.com/71321529/192722336-54016f79-89ef-4c8c-9d71-a6e91ebab03f.jpeg")
S+=IMG("Data Collection Architecture","https://user-images.githubusercontent.com/71321529/192721926-de265f9b-f301-4943-ac7d-948bff7be9a0.jpeg")
S+=IMG("Deployment Architecture","https://user-images.githubusercontent.com/104005791/199660875-c8e63457-432a-44cb-8a95-800870f3da15.png")

S+=H2("Models Used")
S.append(LIST([link("https://www.javatpoint.com/k-means-clustering-algorithm-in-machine-learning","K-Means"),
               link("https://scikit-learn.org/stable/modules/generated/sklearn.linear_model.LogisticRegression.html","LogisticRegression")]))
S.append(Spacer(1,6))
S.append(P("From the above models, after hyperparameter optimization, we selected two models: <b>K-Means</b> for clustering and <b>Logistic Regression</b> for classification, and used them in a pipeline."))
S.append(LIST(["GridSearchCV is used for hyperparameter optimization in the pipeline."]))

S+=H2("src: Main Package Folder")
S.append(P("<b>Components:</b> contains all components of the Machine Learning project"))
S.append(LIST(["Data Ingestion","Data Validation","Data Transformation","Data Clustering","Model Trainer","Model Evaluation","Model Pusher"]))
S.append(Spacer(1,6))
S.append(P("<b>Custom Logger and Exceptions</b> are used in the project for better debugging purposes."))

S+=H2("Conclusion")
S.append(P("This project can be used in real life by users."))
doc.build(S)