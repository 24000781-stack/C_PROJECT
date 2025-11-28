#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// C?u trúc sinh viên
typedef struct SinhVien {
    int maSV;
    char ten[50];
    float dtb;
    struct SinhVien *next;
} SV;

// Hai con tr? ð?u – cu?i
SV *head = NULL;
SV *tail = NULL;

// Hàm t?o SV m?i
SV* taoSV(int ma, const char ten[], float dtb) {
    SV* sv = (SV*)malloc(sizeof(SV));
    sv->maSV = ma;
    strcpy(sv->ten, ten);
    sv->dtb = dtb;
    sv->next = NULL;
    return sv;
}

// Thêm vào cu?i b?ng tail
void themCuoi(SV *sv) {
    if (head == NULL) {
        head = tail = sv;
    } else {
        tail->next = sv;
        tail = sv;
    }
}

// Hàm d?n buffer
void clearBuffer() {
    int c;
    while ((c = getchar()) != '\n' && c != EOF);
}

// ============================
// 1. KH?I T?O 6 D? LI?U M?U
// ============================
void khoiTaoDuLieuMau() {
    const char *tenMau[10] = {
        "Le Binh",
        "Tran Binh",
        "Le Cuong",
        "Thanh Tuan",
        "Chi Nam",
        "Kieu Phuc",
        "Truong An",
        "Mai Trang",
        "Van Tuan",
        "Bui Tuan"
    };

    float dtbMau[10] = {7.5f, 6.8f, 4.2f, 8.0f, 6.4f, 7.0f, 4.9f, 8.5f, 6.8, 7.8f};

    for (int i = 0; i < 10; i++) {
        themCuoi(taoSV(240001 + i, tenMau[i], dtbMau[i]));
    }

    printf("\n>>> Da khoi tao 10 sinh vien mau.\n");
}

// ============================
// 2. NH?P THÊM SINH VIÊN
// ============================
void nhapThemSV() {
    int ma;
    char ten[50];
    float dtb;

    printf("\n=== NHAP THEM SINH VIEN ===\n");

    printf("Nhap ma sinh vien: ");
    if (scanf("%d", &ma) != 1) {
        printf("Nhap sai!\n");
        clearBuffer();
        return;
    }
    clearBuffer();

    printf("Nhap ho ten: ");
    fgets(ten, sizeof(ten), stdin);
    ten[strcspn(ten, "\n")] = '\0';

    printf("Nhap diem trung binh: ");
    if (scanf("%f", &dtb) != 1) {
        printf("Nhap sai!\n");
        clearBuffer();
        return;
    }

    themCuoi(taoSV(ma, ten, dtb));
    printf("\n>>> Da them sinh vien thanh cong!\n");
}

// ============================
// IN DANH SÁCH
// ============================
void inDS() {
    if (head == NULL) {
        printf("\nDanh sach rong!\n");
        return;
    }
    printf("\n%-10s %-25s %-10s\n", "Ma SV", "Ho ten", "Diem TB");
    printf("----------------------------------------------\n");

    SV *p = head;
    while (p != NULL) {
        printf("%-10d %-25s %-10.2f\n", p->maSV, p->ten, p->dtb);
        p = p->next;
    }
}

// ============================
// T?m v? trí theo m?
// ============================
int timViTri(int ma) {
    int vt = 1;
    SV *p = head;
    while (p != NULL) {
        if (p->maSV == ma) return vt;
        p = p->next;
        vt++;
    }
    return -1;
}

// ============================
// T?m theo DTB
// ============================
void timTheoDTB(float dtb) {
    SV *p = head;
    int dem = 0;

    while (p != NULL) {
        if (p->dtb == dtb) {
            if (dem == 0) {
                printf("\nDanh sach sinh vien co DTB = %.2f:\n", dtb);
                printf("%-10s %-25s %-10s\n", "Ma SV", "Ho ten", "Diem TB");
                printf("----------------------------------------------\n");
            }
            printf("%-10d %-25s %-10.2f\n", p->maSV, p->ten, p->dtb);
            dem++;
        }
        p = p->next;
    }

    if (dem == 0) printf("\nKhong co sinh vien co DTB = %.2f\n", dtb);
}

// ============================
// S?p x?p b?ng swap liên k?t
// ============================
void sapXepTheoTen() {
    if (head == NULL || head->next == NULL) return;

    int swapped;

    do {
        swapped = 0;
        SV *prev = NULL;
        SV *cur = head;

        while (cur->next != NULL) {
            SV *next = cur->next;

            if (strcmp(cur->ten, next->ten) > 0) {
                // Hoán ð?i node
                if (prev == NULL)
                    head = next;
                else
                    prev->next = next;

                cur->next = next->next;
                next->next = cur;

                if (cur->next == NULL)
                    tail = cur;

                swapped = 1;
                prev = next;
            } else {
                prev = cur;
                cur = cur->next;
            }
        }
    } while (swapped);

    printf("\n>>> Da sap xep danh sach theo ten (swap lien ket).\n");
}

// ============================
// Xóa DTB < 5
// ============================
void xoaDTBDuoi5() {
    while (head != NULL && head->dtb < 5.0f) {
        SV *tmp = head;
        head = head->next;
        free(tmp);
    }

    if (head == NULL) {
        tail = NULL;
        return;
    }

    SV *p = head;
    while (p->next != NULL) {
        if (p->next->dtb < 5.0f) {
            SV *tmp = p->next;
            p->next = tmp->next;

            if (tmp == tail)
                tail = p;

            free(tmp);
        } else {
            p = p->next;
        }
    }

    printf("\n>>> Da xoa cac sinh vien co DTB < 5.\n");
}

// ============================
// In sinh viên có DTB 7–8
// ============================
void inKhoang7_8() {
    SV *p = head;
    int dem = 0;

    printf("\nSV co DTB tu 7.0–8.0:\n");
    printf("%-10s %-25s %-10s\n", "Ma SV", "Ho ten", "Diem TB");
    printf("----------------------------------------------\n");

    while (p != NULL) {
        if (p->dtb >= 7.0f && p->dtb <= 8.0f) {
            printf("%-10d %-25s %-10.2f\n", p->maSV, p->ten, p->dtb);
            dem++;
        }
        p = p->next;
    }

    if (dem == 0) printf("Khong co SV nao.\n");
}

int main() {
    int chon, ma;
    float dtb;

    // Kh?i t?o d? li?u có s?n
    khoiTaoDuLieuMau();

    do {
        printf("\n========== MENU ==========\n");
        printf("1. Nhap them sinh vien\n");
        printf("2. Tim vi tri theo ma\n");
        printf("3. Tim sinh vien theo DTB\n");
        printf("4. Sap xep theo ten (swap node)\n");
        printf("5. Xoa SV DTB < 5.0\n");
        printf("6. In SV DTB 7.0–8.0\n");
        printf("7. In toan bo danh sach\n");
        printf("0. Thoat\n");
        printf("==========================\n");
        printf("Nhap lua chon: ");
        if (scanf("%d", &chon) != 1) {
            printf("Nhap sai!\n");
            clearBuffer();
            continue;
        }

        switch (chon) {
            case 1: {
                clearBuffer();
                nhapThemSV();
                break;
            }

            case 2: {
                printf("Nhap ma SV: ");
                scanf("%d", &ma);
                int vt = timViTri(ma);
                if (vt == -1) printf("Khong tim thay.\n");
                else printf("SV o vi tri %d\n", vt);
                break;
            }

            case 3: {
                printf("Nhap DTB: ");
                scanf("%f", &dtb);
                timTheoDTB(dtb);
                break;
            }

            case 4: {
                sapXepTheoTen();
                break;
            }

            case 5: {
                xoaDTBDuoi5();
                break;
            }

            case 6: {
                inKhoang7_8();
                break;
            }

            case 7: {
                inDS();
                break;
            }

            case 0: {
                printf("Thoat chuong trinh!\n");
                break;
            }

            default:
                printf("Lua chon sai!\n");
        }

    } while (chon != 0);

    return 0;
}
