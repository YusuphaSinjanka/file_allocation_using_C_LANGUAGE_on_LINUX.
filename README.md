# file_allocation_using_C_LANGUAGE_on_LINUX.
# I used C language on ubuntu to carry out file allocation in operating systems. The output shows time used, number of used blocks, number of wasted blocks, and average fragmentation
IMPLEMENTATION CODE
#include <stdio.h>
#include <stdlib.h>
#include <time.h>
#define MAX_FILES 1000
#define MAX_BLOCKS 10000
#define BLOCK_SIZE 512
int disk_blocks[MAX_BLOCKS];
int used_blocks = 0;
int wasted_blocks = 0;
struct file {
 int start_block;
 int num_blocks;
};
struct file files[MAX_FILES];
int num_files = 0;
void initialize_disk() {
 int i;
 for (i = 0; i < MAX_BLOCKS; i++) {
 disk_blocks[i] = 0;
 }
}
int find_free_blocks(int num_blocks) {
 int i, j, count;
 for (i = 0; i < MAX_BLOCKS - num_blocks; i++) {
 count = 0;
 for (j = i; j < i + num_blocks; j++) {
 if (disk_blocks[j] == 0) {
 count++;
 } else {
 break;
 }
 }
 if (count == num_blocks) {
 return i;
 }
 }
 return -1;
}
void allocate_blocks(int start_block, int num_blocks) {
 int i;
 for (i = start_block; i < start_block + num_blocks; i++) {
 disk_blocks[i] = 1;
 }
 used_blocks += num_blocks;
 wasted_blocks += BLOCK_SIZE * (num_blocks - 1);
}
void deallocate_blocks(int start_block, int num_blocks) {
 int i;
 for (i = start_block; i < start_block + num_blocks; i++) {
 disk_blocks[i] = 0;
 }
 used_blocks -= num_blocks;
 wasted_blocks -= BLOCK_SIZE * (num_blocks - 1);
}
void add_file(int num_blocks) {
 int start_block = find_free_blocks(num_blocks);
 if (start_block == -1) {
 printf("Error: no free blocks for file\n");
 return;
 }
 files[num_files].start_block = start_block;
 files[num_files].num_blocks = num_blocks;
 num_files++;
 allocate_blocks(start_block, num_blocks);
}
void remove_file(int index) {
 deallocate_blocks(files[index].start_block, files[index].num_blocks);
 int i;
 for (i = index; i < num_files - 1; i++) {
 files[i] = files[i + 1];
 }
 num_files--;
}
void print_stats(int time) {
 int i, frag = 0;
 for (i = 0; i < num_files; i++) {
 frag += files[i].num_blocks - 1;
 }
 float avg_frag = (float) frag / num_files;
 printf("Time: %d\n", time);
 printf("Used blocks: %d\n", used_blocks);
 printf("Wasted blocks: %d\n", wasted_blocks);
 printf("Average fragmentation: %.2f\n", avg_frag);
}
int main() {
 int i;
 int time;
 initialize_disk();
 srand(time);
 for (time = 1; time <= 10; time++) {
 // Add some random files
 for (i = 0; i < 10; i++) {
 add_file(rand() % 10 + 1);
 }
 print_stats(time);
 // Remove some random files
 for (i = 0; i < 5; i++) {
 if (num_files > 0) {
 remove_file(rand() % num_files);
 }
 }
 }
 return 0;
}
