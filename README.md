#include <iostream>
#include <string>
#include <vector>
#include <list>
#include <stack>
#include <queue>
#include <algorithm> 
#include <memory> 

using namespace std;


struct TempatParkir {
    string nama;
    string lokasi;
    bool tersedia;
};


class Pengguna {
private:
    string namaPengguna;
    string kataSandi;

public:

    Pengguna(const string& nama, const string& sandi) : namaPengguna(nama), kataSandi(sandi) {}


    string getNamaPengguna() const {
        return namaPengguna;
    }


    string getKataSandi() const {
        return kataSandi;
    }
};


void tampilkanMenuUtama() {
    cout << "===== Menu Utama =====" << endl;
    cout << "1. Registrasi" << endl;
    cout << "2. Login" << endl;
    cout << "3. Keluar" << endl;
    cout << "Masukkan pilihan Anda: ";
}


void registrasiPengguna(vector<shared_ptr<Pengguna>>& pengguna) {
    string nama, sandi;

    cout << "===== Registrasi Pengguna =====" << endl;
    cout << "Masukkan nama pengguna: ";
    cin >> nama;
    cout << "Masukkan kata sandi: ";
    cin >> sandi;


    shared_ptr<Pengguna> penggunaBaru = make_shared<Pengguna>(nama, sandi);
    pengguna.push_back(penggunaBaru);

    cout << "Registrasi berhasil!" << endl;
}


shared_ptr<Pengguna> loginPengguna(vector<shared_ptr<Pengguna>>& pengguna) {
    string nama, sandi;

    cout << "===== Login Pengguna =====" << endl;
    cout << "Masukkan nama pengguna: ";
    cin >> nama;
    cout << "Masukkan kata sandi: ";
    cin >> sandi;


    for (auto& p : pengguna) {
        if (p->getNamaPengguna() == nama && p->getKataSandi() == sandi) {
            return p;
        }
    }

    cout << "Nama pengguna atau kata sandi salah." << endl;
    return nullptr;
}


bool cariTempatParkirHelper(const vector<TempatParkir>& tempatParkir, const string& lokasi, int left, int right) {
    if (left > right) return false;

    int mid = left + (right - left) / 2;
    string lokasiParkir = tempatParkir[mid].lokasi;
    transform(lokasiParkir.begin(), lokasiParkir.end(), lokasiParkir.begin(), ::tolower);

    if (lokasiParkir.find(lokasi) != string::npos && tempatParkir[mid].tersedia) {
        cout << "Tempat parkir ditemukan: " << tempatParkir[mid].nama << " di " << tempatParkir[mid].lokasi << endl;
        return true;
    }

    return cariTempatParkirHelper(tempatParkir, lokasi, left, mid - 1) ||
           cariTempatParkirHelper(tempatParkir, lokasi, mid + 1, right);
}

void cariTempatParkir(const vector<TempatParkir>& tempatParkir) {
    string lokasi;

    cout << "===== Pencarian Tempat Parkir =====" << endl;
    cout << "Masukkan lokasi yang diinginkan: ";
    cin.ignore(); // Mengabaikan karakter newline yang tersisa
    getline(cin, lokasi); // Menggunakan getline untuk menangkap input dengan spasi


    transform(lokasi.begin(), lokasi.end(), lokasi.begin(), ::tolower);

    if (!cariTempatParkirHelper(tempatParkir, lokasi, 0, tempatParkir.size() - 1)) {
        cout << "Tidak ditemukan tempat parkir di lokasi tersebut." << endl;
    }
}


void pesanTempatParkir(vector<TempatParkir>& tempatParkir) {
    string namaTempat;

    cout << "===== Pemesanan Tempat Parkir =====" << endl;
    cout << "Masukkan nama tempat parkir yang ingin dipesan: ";
    cin.ignore(); // Mengabaikan karakter newline yang tersisa
    getline(cin, namaTempat); // Menggunakan getline untuk menangkap input dengan spasi

    stack<TempatParkir*> stackParkir;
    for (auto& tp : tempatParkir) {
        if (tp.nama == namaTempat && tp.tersedia) {
            stackParkir.push(&tp);
        }
    }

    if (!stackParkir.empty()) {
        TempatParkir* tp = stackParkir.top();
        tp->tersedia = false;
        cout << "Pemesanan berhasil!" << endl;
        cout << "Anda akan menerima struk dan barcode di email." << endl;
    } else {
        cout << "Tempat parkir tidak tersedia atau nama salah." << endl;
    }
}

int main() {

    vector<shared_ptr<Pengguna>> pengguna;


    vector<TempatParkir> tempatParkir = {
        {"Parkir A", "Jl. Sudirman", true},
        {"Parkir B", "Jl. Thamrin", true},
        {"Parkir C", "Jl. Gatot Subroto", true},
    };


    bool selesai = false;
    while (!selesai) {
        tampilkanMenuUtama();

        int pilihan;
        cin >> pilihan;

        switch (pilihan) {
            case 1:
                registrasiPengguna(pengguna);
                break;
            case 2: {
                shared_ptr<Pengguna> penggunaLogin = loginPengguna(pengguna);
                if (penggunaLogin != nullptr) {

                    bool logout = false;
                    while (!logout) {
                        cout << "===== Menu Pengguna =====" << endl;
                        cout << "1. Cari Tempat Parkir" << endl;
                        cout << "2. Pesan Tempat Parkir" << endl;
                        cout << "3. Logout" << endl;
                        cout << "Masukkan pilihan Anda: ";

                        int pilihanPengguna;
                        cin >> pilihanPengguna;

                        switch (pilihanPengguna) {
                            case 1:
                                cariTempatParkir(tempatParkir);
                                break;
                            case 2:
                                pesanTempatParkir(tempatParkir);
                                break;
                            case 3:
                                cout << "Logout berhasil." << endl;
                                logout = true;
                                break;
                            default:
                                cout << "Pilihan tidak valid." << endl;
                        }
                    }
                }
                break;
            }
            case 3:
                cout << "Terima kasih!" << endl;
                selesai = true;
                break;
            default:
                cout << "Pilihan tidak valid." << endl;
        }
    }

    return 0;
}
