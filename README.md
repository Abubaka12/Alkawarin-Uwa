```python
from reportlab.lib.pagesizes import inch, letter
from reportlab.pdfgen import canvas
from reportlab.platypus import SimpleDocTemplate, Paragraph, Spacer, PageBreak, HRFlowable
from reportlab.lib.styles import ParagraphStyle, getSampleStyleSheet
from reportlab.lib import colors
from pypdf import PdfWriter
import os

# --- SAITA SUNAYEN FAYILOLI ---
COVER_FILE = "Cover_Temp.pdf"
INTERIOR_FILE = "Interior_Temp.pdf"
FINAL_BOOK = "Alkawarin_Uwa_Cikakke.pdf"

# ==========================================
# 1. SAMAR DA BANGO (COVER GENERATION)
# ==========================================
def create_cover():
    c = canvas.Canvas(COVER_FILE, pagesize=(6*inch*2.1, 9*inch))
    width, height = 6*inch*2.1, 9*inch
    back_x, spine_x, front_x = 0, 6*inch, 6.1*inch
    panel_w, spine_w = 6*inch, 0.1*inch

    # Front Cover
    c.setFillColorRGB(1, 0.92, 0.85)
    c.rect(front_x, 0, panel_w, height, fill=True, stroke=False)
    c.setFillColorRGB(0.36, 0.2, 0.1)
    c.setFont("Times-Bold", 36)
    c.drawCentredString(front_x + panel_w/2, height - 3*inch, "ALKAƘARIN UWA")
    c.setFont("Times-Roman", 18)
    c.drawCentredString(front_x + panel_w/2, height - 3.7*inch, "A Novel of Love, Sacrifice & Destiny")
    c.setFont("Times-Bold", 20)
    c.drawCentredString(front_x + panel_w/2, 1.5*inch, "Abubakar Abdulwahab")

    # Spine
    c.setFillColorRGB(0.85, 0.7, 0.6)
    c.rect(spine_x, 0, spine_w, height, fill=True, stroke=False)

    # Back Cover
    c.setFillColorRGB(0.98, 0.95, 0.90)
    c.rect(back_x, 0, panel_w, height, fill=True, stroke=False)
    c.save()

# ==========================================
# 2. SAMAR DA CIKI (INTERIOR GENERATION)
# ==========================================
def add_footer(canvas, doc):
    canvas.saveState()
    canvas.setFont('Times-Roman', 10)
    canvas.drawCentredString(4.25*inch, 0.5*inch, f"Shafi na {doc.page}")
    canvas.restoreState()

def create_interior():
    doc = SimpleDocTemplate(INTERIOR_FILE, pagesize=letter)
    styles = getSampleStyleSheet()
    elements = []

    # Custom Styles
    title_s = ParagraphStyle('T', fontSize=30, alignment=1, spaceAfter=20)
    normal_s = ParagraphStyle('N', fontSize=12, leading=14, spaceAfter=10)
    section_s = ParagraphStyle('S', fontSize=18, spaceAfter=10, color=colors.brown)

    # Title Page
    elements.append(Spacer(1, 2*inch))
    elements.append(Paragraph("ALKAƘARIN UWA", title_s))
    elements.append(Paragraph("Marubuci: Abubakar Abdulwahab", styles['Heading3']))
    elements.append(PageBreak())

    # Dedication
    elements.append(Spacer(1, 3*inch))
    elements.append(Paragraph("<i>Ga dukkan uwar da ta sadaukar da farin cikinta domin na ɗanta.</i>", title_s))
    elements.append(PageBreak())

    # Content
    story = {
        "PART 1 – Barin Gida": "“Da na yi kudi zan gina miki gida mai hawa biyu!” Hassan ya fadi haka yana barin kauye...",
        "PART 7 – Sirrin Wasiƙa": "A wasiyyar ta ƙarshe ta bayyana cewa ba ita ta haife shi ba. Ta zabe shi a matsayin danta."
    }
    for t, c in story.items():
        elements.append(Pragraph(t, section_s))
        elements.append(HRFlowable(width="100%", thickness=1, color=colors.grey))
        elements.append(Paragraph(c, normal_s))
        elements.append(Spacer(1, 0.3*inch))

    # Author Bio
    elements.append(PageBreak())
    elements.append(Paragraph("Game da Marubuci", section_s))
    elements.append(Paragraph("Abubakar Abdulwahab marubuci ne dake sha'awar adana al'adun Hausawa...", normal_s))

    doc.build(elements, onFirstPage=add_footer, onLaterPages=add_footer)

# ==========================================
# 3. HAƊAWA (MERGING)
# ==========================================
def merge_all():
    merger = PdfWriter()
    merger.append(COVER_FILE)
    merger.append(INTERIOR_FILE)
    with open(FINAL_BOOK, "wb") as f:
        merger.write(f)
    # Share files din wucin gadi
    os.remove(COVER_FILE)
    os.remove(INTERIOR_FILE)
    print(f"AN GAMA! An samar da cikakken littafi: {FINAL_BOOK}")

if __name__ == "__main__":
    create_cover()
    create_interior()
    merge_all()
```
