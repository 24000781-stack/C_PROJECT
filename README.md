#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Cau truc sinh vien
typedef struct SinhVien {
    int maSV;
    char ten[50];
    float dtb;
    struct SinhVien *next;
} SV;

// Tao mot sinh vien moi
SV* taoSV(int ma, char ten[], float dtb) {
    SV* sv = (SV*)malloc(sizeof(SV));
    sv->maSV = ma;
    strcpy(sv->ten, ten);
    sv->dtb = dtb;
    sv->next = NULL;
    return sv;
}

// Them sinh vien vao cuoi danh sach
void themCuoi(SV **head, SV *sv) {
    if (*head == NULL) *head = sv;
    else {
        SV *p = *head;
        while (p->next != NULL)
            p = p->next;
        p->next = sv;
    }
}

// Nhap danh sach sinh vien
void nhapDS(SV **head) {
    int ma; char ten[50]; float dtb;
    printf("\n=== NHAP DANH SACH SINH VIEN ===\n");
    while (1) {
        printf("Nhap ma sinh vien (<=0 de dung): ");
        scanf("%d", &ma);
        if (ma <= 0) break;
        fflush(stdin);
        printf("Nhap ho ten: ");
        fgets(ten, sizeof(ten), stdin);
        ten[strcspn(ten, "\n")] = '\0'; // xoa ky tu xuong dong
        printf("Nhap diem trung binh: ");
        scanf("%f", &dtb);
        themCuoi(head, taoSV(ma, ten, dtb));
    }
}

// In danh sach sinh vien
void inDS(SV *head) {
    if (head == NULL) {
        printf("\nDanh sach rong!\n");
        return;
    }
    printf("\n%-10s %-25s %-10s\n", "Ma SV", "Ho ten", "Diem TB");
    printf("----------------------------------------------\n");
    while (head != NULL) {
        printf("%-10d %-25s %-10.2f\n", head->maSV, head->ten, head->dtb);
        head = head->next;
    }
}

// Tim vi tri sinh vien theo ma
int timViTri(SV *head, int ma) {
    int vt = 1;
    while (head != NULL) {
        if (head->maSV == ma) return vt;
        head = head->next;
        vt++;
    }
    return -1;
}

// Tim sinh vien theo DTB
void timTheoDTB(SV *head, float dtb) {
    int dem = 0;
    while (head != NULL) {
        if (head->dtb == dtb) {
            if (dem == 0) {
                printf("\nCac sinh vien co DTB = %.2f:\n", dtb);
                printf("%-10s %-25s %-10s\n", "Ma SV", "Ho ten", "Diem TB");
                printf("----------------------------------------------\n");
            }
            printf("%-10d %-25s %-10.2f\n", head->maSV, head->ten, head->dtb);
            dem++;
        }
        head = head->next;
    }
    if (dem == 0) printf("\nKhong co sinh vien nao co DTB = %.2f\n", dtb);
}

// Sap xep danh sach theo ten (tang dan)
void sapXepTheoTen(SV **head) {
    if (*head == NULL) return;
    for (SV *i = *head; i->next != NULL; i = i->next) {
        for (SV *j = i->next; j != NULL; j = j->next) {
            if (strcmp(i->ten, j->ten) > 0) {
                // hoan doi noi dung
                int maTam = i->maSV;
                float dtbTam = i->dtb;
                char tenTam[50];
                strcpy(tenTam, i->ten);

                i->maSV = j->maSV;
                i->dtb = j->dtb;
                strcpy(i->ten, j->ten);

                j->maSV = maTam;
                j->dtb = dtbTam;
                strcpy(j->ten, tenTam);
            }
        }
    }
    printf("\nDa sap xep danh sach tang dan theo ten.\n");
}

// Xoa sinh vien co DTB < 5.0
void xoaDTBDuoi5(SV **head) {
    while (*head != NULL && (*head)->dtb < 5.0) {
        SV *tmp = *head;
        *head = (*head)->next;
        free(tmp);
    }
    SV *p = *head;
    while (p != NULL && p->next != NULL) {
        if (p->next->dtb < 5.0) {
            SV *tmp = p->next;
            p->next = tmp->next;
            free(tmp);
        } else {
            p = p->next;
        }
    }
    printf("\nDa xoa sinh vien co DTB < 5.0.\n");
}

// In sinh vien co diem tu 7.0 den 8.0
void inKhoang7_8(SV *head) {
    int dem = 0;
    printf("\nDanh sach sinh vien co DTB tu 7.0 den 8.0:\n");
    printf("%-10s %-25s %-10s\n", "Ma SV", "Ho ten", "Diem TB");
    printf("----------------------------------------------\n");
    while (head != NULL) {
        if (head->dtb >= 7.0 && head->dtb <= 8.0) {
            printf("%-10d %-25s %-10.2f\n", head->maSV, head->ten, head->dtb);
            dem++;
        }
        head = head->next;
    }
    if (dem == 0)
        printf("Khong co sinh vien nao trong khoang nay.\n");
}

// Ham chinh
int main() {
    SV *head = NULL;
    int chon, ma;
    float dtb;

    do {
        printf("\n================ MENU QUAN LY SINH VIEN ================\n");
        printf("1. Tao danh sach sinh vien\n");
        printf("2. Tim vi tri theo ma sinh vien\n");
        printf("3. Tim sinh vien theo DTB\n");
        printf("4. Sap xep danh sach theo ten\n");
        printf("5. Xoa sinh vien co DTB < 5.0\n");
        printf("6. In sinh vien co DTB tu 7.0 den 8.0\n");
        printf("7. In toan bo danh sach\n");
        printf("0. Thoat chuong trinh\n");
        printf("========================================================\n");
        printf("Nhap lua chon: ");
        scanf("%d", &chon);

        switch (chon) {
            case 1:
                nhapDS(&head);
                break;

            case 2: {
                printf("Nhap ma sinh vien can tim: ");
                scanf("%d", &ma);
                int vt = timViTri(head, ma);
                if (vt == -1)
                    printf("Khong tim thay sinh vien co ma %d\n", ma);
                else
                    printf("Sinh vien co ma %d nam o vi tri thu %d\n", ma, vt);
                break;
            }

            case 3: {
                printf("Nhap diem trung binh can tim: ");
                scanf("%f", &dtb);
                timTheoDTB(head, dtb);
                break;
            }

            case 4:
                sapXepTheoTen(&head);
                break;

            case 5:
                xoaDTBDuoi5(&head);
                break;

            case 6:
                inKhoang7_8(head);
                break;

            case 7:
                inDS(head);
                break;

            case 0:
                printf("\nThoat chuong trinh. Tam biet!\n");
                break;

            default:
                printf("\nLua chon khong hop le. Vui long nhap lai!\n");
        }

    } while (chon != 0);

    return 0;
}
