if(ROOT_roofit_FOUND)
  ROOTTEST_ADD_TEST(read-scientificnotation
                    MACRO read_scientific_notation.py
                    OUTCNVCMD grep -v -e "Wouter"
                    OUTREF read_scientific_notation.ref)

  ROOTTEST_ADD_TEST(RooDataSet_ASCII_in
                    MACRO ${CMAKE_CURRENT_SOURCE_DIR}/ASCII-in-out.C
                    MACROARG "false, \"${CMAKE_CURRENT_SOURCE_DIR}/ASCII-in-out_data.txt\"")
  ROOTTEST_ADD_TEST(RooDataSet_ASCII_out
                    MACRO ${CMAKE_CURRENT_SOURCE_DIR}/ASCII-in-out.C
                    MACROARG "true, \"./ASCII-in-out_result.txt\""
                    ${WILLFAIL_ON_WIN32}
                    POSTCMD diff ${CMAKE_CURRENT_SOURCE_DIR}/ASCII-in-out_data.txt ./ASCII-in-out_result.txt)

  if(MSVC)
    ROOT_ADD_GTEST(stabilityTests stabilityTests.cxx LIBRARIES libCore libTree libRIO libRooFitCore libRooFit
                   COPY_TO_BUILDDIR ${CMAKE_CURRENT_SOURCE_DIR}/stabilityTests_data_1.root)
  else()
    ROOT_ADD_GTEST(stabilityTests stabilityTests.cxx LIBRARIES ROOT::RooFit
                   COPY_TO_BUILDDIR ${CMAKE_CURRENT_SOURCE_DIR}/stabilityTests_data_1.root)
  endif()

  ROOT_ADD_GTEST(loadOldWorkspace loadOldWorkspace.cxx LIBRARIES ROOT::RooFit
                 COPY_TO_BUILDDIR
                 ${CMAKE_CURRENT_SOURCE_DIR}/rf502_workspace_v5.34.root
                 ${CMAKE_CURRENT_SOURCE_DIR}/rf502_workspace_v6.04.root
                 ${CMAKE_CURRENT_SOURCE_DIR}/rf502_workspace_v6.14.root
                 )

  if(NOT MSVC OR win_broken_tests)
    add_subdirectory(vectorisedPDFs)
  endif()
endif()
